---
layout:  post
title:   Fragment模拟Tab以及简单优化
date:   2015-06-25 11:23:02
author:  "张耀文"
catalog:   false
tags:
    - android

---
一、前言  
在郭霖大大的博客（ [ http://blog.csdn.net/guolin_blog/article/details/13171191](http://blog.csdn.net/guolin_blog/article/details/13171191) ）的基础上试着进行代码优化。  
二、分析  
有4个tab页：message，contacts，news，setting。每个tab页的点击事件流程基本一致。相应的ui组件命名规则一致（都以tab名为前缀）。  
原文用switch语句写了4遍点击事件（作为给新手看的教程无可厚非），作为一个程序猿，试着来优化一下。  
三、优化前准备  
1 使用数组存放tab名，用到tab名的地方从数组取。

    
    
    String tabs[] = {"message", "contacts", "news", "setting"};

2 ui组件也和tab名相关，怎么根据数组tabs获取ui呢？使用 ` getResources().getIdentifier `
（相比直接用findViewById耗费些许性能）

    
    
    //name为ui的id名，type传入"id"
     private View findView(String name, String type) {
            int resId = getResources().getIdentifier(name, type, "com.shadow.zyw.sdu.fragmenttabs");
            Log.d("test", "find view:" + name + resId);
            View view = (View) findViewById(resId);
            return view;
        }

3 对于xml布局文件和图片资源等，需要用java发射机制

    
    
        //mipmap下图片资源id
        private Integer findImage(String name) {
            Integer value = null;
            Class<R.mipmap> cls = R.mipmap.class;
            try {
                value = cls.getDeclaredField(name).getInt(null);
            } catch (Exception e) {
                Log.d("test", "findimage error" + name);
            }
            return value;
    
        }
    
    
        //返回布局文件id
         private Integer findLayout(String name) {
            Integer value = null;
            Class<R.layout> cls = R.layout.class;
            try {
                value = cls.getDeclaredField(name).getInt(null);
            } catch (Exception e) {
                Log.d("test", "findlayout error" + name);
            }
            return value;
    
        }

四、优化开始  
1 对于tabs绑定监听器可以使用for循环

    
    
            for (int i = 0; i < tabs.length; i++) {
                View view = findView(tabs[i] + "_layout", "id");            
                view.setOnClickListener(this);
                //将id和tas名保存，可以知道哪个控件被点击
                map.put(String.valueOf(view.getId()), tabs[i]);
            }

2 fragment对象的生成：有2种方式  
（1）使用java发射机制，动态根据类名生成对象

    
    
        private static Object newInstance(String className) throws Exception {
            //类名首字母大写
            StringBuilder sb = new StringBuilder(className);
            sb.setCharAt(0, Character.toUpperCase(sb.charAt(0)));
            className = sb.toString();
            Log.d("test", "classname:" + className);
            Class clazz = Class.forName("com.shadow.zyw.sdu.fragmenttabs." + className);
            return clazz.newInstance();
    
        }
    
    
     fragment = (Fragment) newInstance(name + "Frag");

（2）在fragment的onCreateView中根据tabs名，引入不同的布局文件。（感觉这样灵活性很差，这里只是练手，扩展思路）

    
    
    @Override
        public View onCreateView(LayoutInflater inflater, ViewGroup container, Bundle savedInstanceState) {
    
            View view = inflater.inflate(findLayout(xml), container, false);
            Log.d("test", "frag create" + xml);
            return view;
        }
    
    
            fragObject = new FragObject();
            fragObject.setXml(name + "_layout");

3 onclick事件

    
    
      @Override
        public void onClick(View v) {
            FragmentTransaction fragmentTransaction = fragmentManager.beginTransaction();
            Log.d("test", String.valueOf(v.getId()));
            String name = map.get(String.valueOf(v.getId()));
            Log.d("test", "name" + name);
    
            fragObject = new FragObject();
            fragObject.setXml(name + "_layout");
            try {
                fragment = (Fragment) newInstance(name + "Frag");    
    
            } catch (Exception e) {
                e.printStackTrace();
                Log.d("test", "cannot create class:" + name);
                return;
            }
            ImageView imageView = (ImageView) findView(name + "_image", "id");
            imageView.setImageResource(findImage(name + "_selected"));
            TextView textView = (TextView) findView(name + "_text", "id");
            textView.setTextColor(Color.WHITE);
    
            fragmentTransaction.add(R.id.content, fragment);
            // fragmentTransaction.add(R.id.content, fragObject);
            fragmentTransaction.commit();
    
        }

4 判断是否已经产生过fragment，清空fragment等，使用Map保存fragment对象

    
    
            FragmentTransaction fragmentTransaction = fragmentManager.beginTransaction();
            if (fragmentMap.get(name) == null) {
                try {
                    fragment = (Fragment) newInstance(name + "Frag");
                    fragInterface = (FragInterface) newInstance(name + "Frag");
    
                } catch (Exception e) {
                    e.printStackTrace();
                    Log.d("test", "cannot create class:" + name);
                    return;
                }
                fragmentMap.put(name, fragment);
                fragmentTransaction.add(R.id.content, fragment);
            } else {
                fragment = fragmentMap.get(name);
                fragmentTransaction.show(fragment);
            }
            fragmentTransaction.commit();

5 清空点击状态

    
    
        private void clearSelected() {
            for (int i = 0; i < tabs.length; i++) {
                ImageView imageView = (ImageView) findView(tabs[i] + "_image", "id");
                imageView.setImageResource(findImage(tabs[i] + "_unselected"));
                TextView textView = (TextView) findView(tabs[i] + "_text", "id");
                textView.setTextColor(Color.parseColor("#82858b"));
                FragmentTransaction fragmentTransaction = fragmentManager.beginTransaction();
                if (fragmentMap.get(tabs[i]) != null) {
                    fragmentTransaction.hide(fragmentMap.get(tabs[i]));
                }
    
                fragmentTransaction.commit();
    
            }
        }

6 优化后

    
    
     @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);
            fragmentManager = getFragmentManager();
            for (int i = 0; i < tabs.length; i++) {
                View view = findView(tabs[i] + "_layout", "id");
                map.put(String.valueOf(view.getId()), tabs[i]);
                view.setOnClickListener(this);
            }
            setSelected(tabs[0]);
        }
    
        @Override
        public void onClick(View v) {
            Log.d("test", String.valueOf(v.getId()));
            String name = map.get(String.valueOf(v.getId()));
            Log.d("test", "name" + name);
    
            clearSelected();
            setSelected(name);
    
        }
    
        private void clearSelected() {
            for (int i = 0; i < tabs.length; i++) {
                ImageView imageView = (ImageView) findView(tabs[i] + "_image", "id");
                imageView.setImageResource(findImage(tabs[i] + "_unselected"));
                TextView textView = (TextView) findView(tabs[i] + "_text", "id");
                textView.setTextColor(Color.parseColor("#82858b"));
                FragmentTransaction fragmentTransaction = fragmentManager.beginTransaction();
                if (fragmentMap.get(tabs[i]) != null) {
                    fragmentTransaction.hide(fragmentMap.get(tabs[i]));
                }
    
                fragmentTransaction.commit();
    
            }
        }
    
        private void setSelected(String name) {
            FragmentTransaction fragmentTransaction = fragmentManager.beginTransaction();
            if (fragmentMap.get(name) == null) {
                try {
                    fragment = (Fragment) newInstance(name + "Frag");
                    fragInterface = (FragInterface) newInstance(name + "Frag");
    
                } catch (Exception e) {
                    e.printStackTrace();
                    Log.d("test", "cannot create class:" + name);
                    return;
                }
                fragmentMap.put(name, fragment);
                fragmentTransaction.add(R.id.content, fragment);
            } else {
                fragment = fragmentMap.get(name);
                fragmentTransaction.show(fragment);
            }
            fragmentTransaction.commit();
            ImageView imageView = (ImageView) findView(name + "_image", "id");
            imageView.setImageResource(findImage(name + "_selected"));
            TextView textView = (TextView) findView(name + "_text", "id");
            textView.setTextColor(Color.WHITE);
        }

