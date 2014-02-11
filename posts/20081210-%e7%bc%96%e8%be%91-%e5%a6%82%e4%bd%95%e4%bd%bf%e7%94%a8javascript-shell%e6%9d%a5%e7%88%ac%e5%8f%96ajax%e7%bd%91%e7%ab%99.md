title: [编辑] 如何使用javascript shell来爬取ajax网站
link: http://www.54chen.com/life/%e7%bc%96%e8%be%91-%e5%a6%82%e4%bd%95%e4%bd%bf%e7%94%a8javascript-shell%e6%9d%a5%e7%88%ac%e5%8f%96ajax%e7%bd%91%e7%ab%99.html
author: cc0cc
description: 
post_id: 340
created: 2008/12/10 14:15:49
created_gmt: 2008/12/10 06:15:49
comment_status: open
post_name: %e7%bc%96%e8%be%91-%e5%a6%82%e4%bd%95%e4%bd%bf%e7%94%a8javascript-shell%e6%9d%a5%e7%88%ac%e5%8f%96ajax%e7%bd%91%e7%ab%99
status: private
post_type: post

# [编辑] 如何使用javascript shell来爬取ajax网站

**\--by 周海维** 最近帮口碑的同事写了一个抓取校内网高中列表的js脚本，介绍一下对付ajax的网站的js脚本。也可以用来作ajax自动化测试和死链发现。 javascript shell 可以在<https://www.squarefree.com/bookmarklets/webdevel.html> 页面中找到。拖拽shell按钮到书签工具栏，以后每次点击书签工具栏的shell，就会弹出一个交互式 shell。 ajax网站的典型链接是通过onclick属性来实现动态加载数据和显示的。通常onclick动作中会使用XMLHttpRequest来进行异步的数据加载，但是我们很难取得数据加载成功并处理完成的事件。所以如果要进行这类网页的抓取，我们需要设置定时器来控制抓取的时机。但是javascript不支持continuation，所以必须把动作分解成一个支持迭代的函数。 我们把抓取xiaonei.com的一个高中注册信息作为例子。 我们感兴趣的东西在下面几个UL中： 

    id="popup-province"，表示省份的列表 
    id="popup-city"，表示城市的列表 
    id="schoolCityQuList"，表示区域的列表 
    id="schoolListContentUl"，表示学校的列表 
正常的考虑是，先选择省份，再选城市，再选择区域，最后选择学校。这个程序的目的是拿到每个学校列表，并输入对应的省份城市区域信息。 用浏览器打开<http://xiaonei.com/MyEducationInfo.do#nogo> ，然后打开一个javascript shell。把附录中的程序粘贴到这个shell里面，执行go()就可以爬取高中列表。运行的时候，可以看到好像有一个幽灵鼠标在点击省份，再点击城市，然后选择区域。 附录的脚本也可以作为ajax网站的自动化测试脚本，还可以测试ajax 的死链。例如，在校内网上，点击黑龙江的伊春市，永远显示的是“正在读取数据”，明显是数据读取时发生了问题。 通常的实现可能是 while(not finished) { 

    select_province(); 
    sleep(3000); 
    select_city(); 
    sleep(2000); 
    select_area(); 
    sleep(1000); 
    print_school(); 
} 但是，javascript不支持sleep函数。所以需要把select_province等几个函数改写成支持迭代的工作方式。作为一个很像lisp的语言，javascript提供了firstclass function，closure。世界因此变得美好。利用这两个特性，我们可以模拟sleep，把每个迭代函数作为任务，放到任务队列里，然后定时调度。 每次调用迭代函数，返回0表示此次迭代结束，返回正整数表示，下个动作需要等待返回值毫秒。但是每次迭代之前，我们都需要生成迭代函数，下面这个函数生成了一个选择省份的迭代函数。每次执行这个迭代函数，浏览器会模拟点击下一个省份，同时，把下一个动作选择城市，放到任务堆栈里面。 function province(data) { 

    var first = 1; 
    var i = 0; 
    return function () 

    { 
    print("!p"); 
    var ele = document.getElementById("popup-province"); 
    if(!ele) { 

    print("!!province is null..."); 
    return 0; 
    } 
    var anchors = ele.getElementsByTagName("a"); 
    if(i < anchors.length) 
    { 
    anchors.item(i).onclick(); 
    print("" + i + anchors.item(i).textContent ); stack.push(city(data + " " + anchors.item(i).textContent)); i ++; return 4000; } if ((anchors.length == 0) && first) { stack.push(city(data + " " )); first = 0; return 4000; } 
    return 0; 
    } 
} closure的美好之处就是在于，每一个closure产生的所有变量都都不会随着函数调用结束而消失，如果在c语言里，就像是匿名的全局变量。这些变量会一直存在，直到他们被作为垃圾回收起来。上面那个函数的first和i，就是这种变量。closure的优美之处还在于，由于没有引进全局变量，函数的副作用限制在函数内部。 javascript世界里如果有lisp那样的macro，会变得更美好，这个脚本也只会有现在三分之一的长度。 >>>>>>>> 8< >>>>>>>>>> // xiaonei.com high school list crawler // feel free to send your comments to haiwei.zhou@alibaba-inc.com var stack = []; function school(data) { 

    return function () 
    { 

    print("!s"); 
    var ele = document.getElementById("schoolListContentUl"); 
    if(!ele) 
    { 

    print("!null school"); 
    return 0; 
    } 
    var anchors = ele.getElementsByTagName("a"); 
    for (var i = 0; i< anchors.length; i++) 
    { 

    print(data + " " + anchors.item(i).textContent + "\n"); 
    } 
    return 0; 
    } 
} function tabpan_print(data) { 

    return function () 
    { 

    print("!s"); 
    var ele = document.getElementById("schoolCityQuList"); 
    if(!ele) 
    { 

    print("!null tabpan"); 
    return 0; 
    } 
    var anchors = ele.getElementsByTagName("a"); 
    for (var i = 0; i< anchors.length; i++) 
    { 

    print(data + " " + anchors.item(i).textContent + "\n"); 
    } 
    return 0; 
    } 
} function tabpan(data) { 

    var first = 1; 
    var i = 0; 
    return function () 
    { 

    print("!t"); 
    var ele = document.getElementById("schoolCityQuList"); 
    if(!ele) 
    { 

    print("!null tabpan"); 
    return 0; 
    } 
    var anchors = ele.getElementsByTagName("a"); 
    if(i < anchors.length) 
    { 

    anchors.item(i).onclick(); 
    print ("" + i + anchors.item(i).textContent); stack.push(school(data + " " + anchors.item(i).textContent)); 
    i ++; 
    return 2000; 
    } 
    if ((anchors.length == 0) && first) 
    { 

    stack.push(school(data + " ")); 
    first = 0; 
    return 2000; 
    } 
    return 0; 
    } 
} function city(data) { 

    var i = 0; 
    var first = 1; 
    return function () 
    { 

    print("!c"); 
    var ele = document.getElementById("popup-city"); 
    if(!ele) { 

    print("!!city is null..."); 
    return 0; 
    } 
    var anchors = ele.getElementsByTagName("a"); 
    if(i < anchors.length) 
    { 

    anchors.item(i).onclick(); 
    print ("" + i + anchors.item(i).textContent); // stack.push(tabpan(data + " " + anchors.item(i).textContent)); stack.push(tabpan_print(data + " " + anchors.item(i).textContent)); i ++; return 3000; } if ((anchors.length == 0) && first ) { // stack.push(tabpan(data + " ")); stack.push(tabpan_print(data + " ")); first = 0; return 2000; 
    } 
    return 0; 
    } 
} function province(data) { 

    var first = 1; 
    var i = 0; 
    return function () 
    { 

    print("!p"); 
    var ele = document.getElementById("popup-province"); 
    if(!ele) { 

    print("!!province is null..."); 
    return 0; 
    } 
    var anchors = ele.getElementsByTagName("a"); 
    if(i < anchors.length) 
    { 

    anchors.item(i).onclick(); 
    print("" + i + anchors.item(i).textContent ); stack.push(city(data + " " + anchors.item(i).textContent)); i ++; return 4000; } if ((anchors.length == 0) && first) { stack.push(city(data + " " )); first = 0; return 4000; }