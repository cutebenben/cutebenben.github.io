---
layout: post
title: 'AJAX+ThinkPHP实现选择题批改'
subtitle: 'AJAX+ThinkPHP实现选择题批改'
date: 2018-01-19
categories: 技术
cover: 'https://warrest.github.io/wArrest.github.io/assets/phpFrame/u=228695038,594732975&fm=27&gp=0.jpg'
tags: AJAX ThinkPHP jq
---

前几天，和好久不见的色鬼叙了叙旧，得知他正在做毕业设计，其中一个要实现的功能就是在线选择题批改，他说有点犯难，巧的是他正在学TP。正好我也学习了一个学期的TP，感觉这个功能还挺有趣的，就主动说帮忙写，其中碰到不少问题，不过最终还是解决了。
<h2>123</h2>
<iframe src="https://skydrive.live.com/embed?cid=8B504C1595CD3973&amp;resid=8B504C1595CD3973%2126382&amp;authkey=AJzDcN30q6g4W0Y&amp;em=2" width="700px" height="500px" frameborder="0" scrolling="no"> </iframe>

### 功能简述：一个账户登录，随机从题库中抽取10道选择题，开始考试，提交，批改分数。

### 思路：
        

1.首先准备三张表：

**question表**：![TIM截图20180118100240](\assets\img\testdemo\TIM截图20180118100240.png)

user表：![TIM截图20180118100316](\assets\img\testdemo\TIM截图20180118100316.png)

user-test表（存放对应用户，对应用户的十道题目，也就是保存用户的试卷表）![TIM截图20180118100339](\assets\img\testdemo\TIM截图20180118100339.png)

第一步，用户登录：

index_index.html:

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
    <form action="{:U('Index/question')}" method="post">
        账号<input type="text" name="email">
        密码 <input type="password" name="password">
        <input type="submit">
    </form>
</body>


</html>
```

账号密码POST传到控制器中的check方法

```php
public function question(){
        $user=M('user');//实例化用户表
        $question=M('question');//实例化题库表
        $test=M('user-test');//实例化试卷表
        //print_r($_POST);
        if(isset($_POST)){   //如果接收到POST数据
            $email=$_POST['email'];
            $password=$_POST['password'];
            $sql2="select password from user where email='".$email."'";
            $rpassword=$user->query($sql2);
            if($password==$rpassword['0']['password']) {//如果密码正确,执行以下操作
                echo '<script>alert("密码正确!");</script>';
                $sql = "select * from question order by rand() limit 10;";
                $res1 = $question->query($sql);//随机取出10道题目
                $sql1 = "select user_id from user where email='" . $email . "'";
                $res2 = $user->query($sql1);//取出用户id，我这里直接又去user表查了一次，实际项目中可以直接调用session，获取用户id
                for ($i = 0; $i < 10; $i++) {
                    $data['qid'] = $res1[$i]['id'];
                    $data['question'] = $res1[$i]['question'];
                    $data['A'] = $res1[$i]['a'];
                    $data['B'] = $res1[$i]['b'];
                    $data['C'] = $res1[$i]['c'];
                    $data['D'] = $res1[$i]['d'];
                    $data['right'] = $res1[$i]['right'];
                    $data['uid'] = $res2['0']['user_id'];
                    $test->add($data);


                }
                //遍历存入user-test(试卷表)
                echo "<script>window.location.href='http://localhost/test/index.php/home/index/content.html';</script>";//跳转到答题页面，这里也可以用TP的$this->redirect(）函数；

            }else{
                echo '<script>alert("密码错误！");history.go(-1);</script>';

            }
        }

    }
```

虽然说用了TP但是写法还是比较传统，应该有更简洁明了的写法，不过应该是比较容易看懂的

带着一步位置，用户登录取出了10道题目，存入了试卷表：

如图：

![TIM截图20180118102026](\assets\img\testdemo\TIM截图20180118102026.png)

res字段现在是空的也就用户还没有完成题目

第二部用户，进入答题页面：

Index_content.html

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport"
          content="width=device-width, user-scalable=no, initial-scale=1.0, maximum-scale=1.0, minimum-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <link href="__PUBLIC__/img/title/favicon.ico" type="image/x-icon" rel="shortcut icon" />
    <link rel="stylesheet" href="__PUBLIC__/css/bootstrap.css">
    <link rel="stylesheet" href="__PUBLIC__/css/my.css">
    <link rel="stylesheet" href="__PUBLIC__/font/font-awesome.min.css">

    <title>文章分类</title>
</head>
<body>
<div class="container">
    <volist name="res" id="vo" key="k">
        <h4>{$k}.{$vo.question}</h4>
        <form action="__CONTROLLER__/check">
            <label>A<input type="radio" name="{$vo.id}" value="A">{$vo.a}</label>&nbsp&nbsp&nbsp <br>
            <label>B<input type="radio" name="{$vo.id}" value="B">{$vo.b}</label>&nbsp&nbsp&nbsp <br>
            <label>C<input type="radio" name="{$vo.id}" value="C">{$vo.c}</label>&nbsp&nbsp&nbsp <br>
            <label>D<input type="radio" name="{$vo.id}" value="D">{$vo.d}</label>&nbsp&nbsp&nbsp <br>

        </form>
        <br>
    </volist>
<button id="bt">交卷！</button>
    <a href="__CONTROLLER__/res" ><button id="bt2" disabled>查看分数</button></a>
</div>



</body>
<script src="__PUBLIC__/js/bootstrap.min.js"></script>
<script src="https://cdn.bootcss.com/jquery/1.12.4/jquery.min.js"></script>
<script>
    $('#bt').click(function () {
        alert('提交成功！可查看分数');
        $('input:checked').each(function () {
            var fth=$(this);
            $.ajax({
                url:"__CONTROLLER__/res",
                type:"POST",
                data:{"id":this.name ,"res":fth.val()},
                success:function (msg) {


                }
            });

        });
    });
    $('#bt').click(function(){
        //逻辑........
        setDisable();
    });

    function setDisable ()
    {
        setTimeout(function(){
            //10秒后移除第二个按钮disabled属性
            $('#bt2').removeAttr("disabled");
        },2000);
    }
</script>
<script src="__PUBLIC__/js/slider.js" type="text/javascript"></script>
</html>
```

打印题目：

```php
public function content(){
        $test=M('user-test');
        $res=$test->select();
        $this->assign('res',$res);
        $this->display();
    }
```

Ajax获取数据将答案传到，更新了对应试卷表（user-test）的res字段

接收答案,更新res字段

```php
public function res(){
        $test=M('user-test');
        $test->res = $_POST['res'];
        $test->where('id='.$_POST['id'])->save();

    }
```

批改

```php
public function res(){
        $test=M('user-test');
        $res=$test->select();
        $sum=0;
        for($i=0;$i<sizeof($res);$i++){

            if($res[$i]['res']==$res[$i]['right']){
                $sum=$sum+10;
            }
        }
        if($sum>=60){
            echo '<h1 style="text-align:center; margin-top: 10%;">恭喜你及格了！你的分数为：'.$sum.'分</h1>';
        }else{
            echo '<h1 style="text-align: center;margin-top: 10%;">你的分数为:<font color="red">'.$sum.'</font>分再接再厉！</h1>';
        }

        $this->display();
    }
```

点击提交就ok了，之后完善的话一个用户考完试就不能进入考试页面，需要给一个状态字段，就可以实现，这里就不详述了。

![TIM截图20180118103408](\assets\img\testdemo\TIM截图20180118103408.png)

![TIM截图20180118103439](\assets\img\testdemo\TIM截图20180118103439.png)

在线的例子改天上传，阿里云的备案一直过不了，很烦。
