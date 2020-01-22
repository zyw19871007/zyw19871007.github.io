---
layout:  post
title:   ListView setSelection 无效问题
date:   2015-06-09 15:54:57
author:  "张耀文"
catalog:   false
tags:

---
一、api

    
    
        /**
         * Sets the currently selected item. If in touch mode, the item will not be selected
         * but it will still be positioned appropriately. If the specified selection position
         * is less than 0, then the item at position 0 will be selected.
         *
         * @param position Index (starting at 0) of the data item to be selected.
         * 手动设置列表中被选择的项目。在触摸模式下，定位到被选择的项目（列表太长有滚动条，使视界外项目可见。）
         */
        @Override
        public void setSelection(int position) {
            setSelectionFromTop(position, 0);
        }
    

二、无效情况  
在有其他ui操作或某些操作使ListView发生变化时，setSelection失效。  
例如：  
![这里写图片描述](http://img-blog.csdn.net/20150609155147383)  
上面是个ListView，下面是EditText和Button。点击发送，EditView内容加入到ListVIew中。使用setSelection定位到最后一项。  
关键逻辑：  
a：取出EditText内容  
b：清空EditText  
c：添加到ListView  
d：调用adapter.notifyDataSetChanged();  
e：调用setSelection  
EditText内容大于1行时，发现问题。  
（1）一开始点击发送后， **不清空** EditText。正常。  
![这里写图片描述](http://img-blog.csdn.net/20150609155505357)

（2） **清空** EditText。不正常。最后一项没有显示。猜测是清空EditText导致界面发生变化，使setSelection失效。  
![这里写图片描述](http://img-blog.csdn.net/20150609155813946)

三、解决方法  
（1）

    
    
    //获得焦点
    listView.requestFocusFromTouch();
    listView.setSelection(list.size() - 1);
    //EditText获得焦点
    editText.requestFocus();

（2）

    
    
    //使用消息队列
    listView.post(new Runnable() {
        @Override
        public void run() {
            listView.setSelection(list.size()-1);
        }
    });
    

（3）

    
    
    //不使用setSelection而使用另一个方法
    listView.smoothScrollToPosition(list.size()-1);

smoothScrollToPosition api

    
    
        /**
         * Smoothly scroll to the specified adapter position. The view will
         * scroll such that the indicated position is displayed.
         * @param position Scroll to this adapter position.
         */
        @android.view.RemotableViewMethod
        public void smoothScrollToPosition(int position) {
            super.smoothScrollToPosition(position);
        }

