> Alfred： 你在这几天的日子里，做了很多练习，其中不免犯错。今天，我们请了很多讲代码的“老师”。可别小瞧他们！它们可不一般，它们都来自于你的代码。请它们来给你上这堂“纠错”课，效果可不一般哦！ 先有请老师们：

Teachers
--------

### Javascripts，from PartyBid

1. 这是一个教你如何消if的实例 Javascript:

```
    SignUpMessage.send_message_on_repeat = function (message_type, message_phone, message_name) {
    if (message_type == "bm") {
        if (Activity.is_has_running()) {
            if (SignUpMessage.is_repeat(message_phone)) {
                native_accessor.send_sms(message_phone, "已收到报名信息，请勿重复报名");
            }
            else {
                var signUpMessage = new SignUpMessage()
                signUpMessage.save();
                native_accessor.send_sms(message_phone, "恭喜!报名成功");
                var wrappers = angular.element(document.getElementById('wrappers')).scope();
                wrappers.$apply(function () {
                    wrappers.refresh();
                });
            }
        }
        else {
            if (Activity.find_activity_state() && Activity.find_activity_state() === "stop") {
                native_accessor.send_sms(message_phone, "Sorry,活动报名已结束");
            }
            if (Activity.find_activity_state() && Activity.find_activity_state() === "un_start") {
                native_accessor.send_sms(message_phone, "活动尚未开始,请稍后");
            }
        }
    }
    else {
        native_accessor.send_sms(message_phone, "短信发送格式不属于报名范畴!");
    }
}
```

改为：

```
Javascript:
var message_type_varier = {
        bm: function () {
            if (Activity.is_has_running()) {
                var message_name = trim_message.slice(2);
                return process_sign_up_message(message_phone, message_name);
            }
            if (Activity.find_choosed_activity()) {
                var statement = {
                    "stop": "Sorry,活动已结束",
                    "un_start": "Sorry,活动尚未开始"
                };
                return native_accessor.send_sms(message_phone, statement[Activity.find_choosed_activity().state]);
            }
            native_accessor.send_sms(message_phone, "Sorry,活动尚未开始或已结束");
        }
    };


    if (message_type_varier[message_type]) {
       return message_type_varier[message_type]();
    }
    native_accessor.send_sms(message_phone, "短信发送格式不属于报名范畴!");
```
2. 这是一个教你如何消除嵌套if的代码：

Javascript:

```
$scope.total_number = 0;
        if (is_sms_belongs_activity_has_signed_yet())
        {
            $scope.in_signed_state = "signed_yet_or_ing";
            if ($scope.signUpMessages) {
                $scope.signUpMessageNumber =    $scope.signUpMessages.length;
            } else {
                $scope.signUpMessageNumber = "0";
            }
        }
        else if (localStorage['state'] == "running") //假如该活动在报名，字样显示人数
        {
            $scope.in_signed_state = "signed_yet_or_ing";
            if ($scope.signUpMessages) {
                $scope.signUpMessageNumber =    $scope.signUpMessages.length;
            } else {
                $scope.signUpMessageNumber = "0";
            }
        }
        else//假如该活动既没存数据也不在报名的时候，都不会显示人数
        {
            $scope.in_signed_state = "not_signed";
        }
```

改好的代码：

```
Javascript:


            if($scope.signUpMessages) {
                $scope.total_number = $scope.signUpMessages.length;
                return $scope.show_which_title = "show_number";
            }
            if(Activity.is_choosed_running())
            {
                $scope.total_number = 0;
                return $scope.show_which_title = "show_number";
            }
            $scope.show_which_title = "none_number";
```
3. 这个例子会教你把true放在“”里，记得要加Boolean，呵呵呵…… 重点在： activity.is_choosed === "true";

Javascript:

```
Activity.find_choosed_activity = function () {
   return  _(Activity.get_activities()).find(function (activity) {
       return activity.is_choosed === "true";
   });
};
```

改过的代码

Javascript:

```
Activity.find_choosed_activity = function () {
    return  _(Activity.get_activities()).find(function (activity) {
        return activity.is_choosed === true;
    });
};
```
4. 这个实例教会你_ .where 不同于 _. find，要看清楚Doc再用。

Javascript:

```
SignUpMessage.get_messages_activity_running = function () {
    return _(SignUpMessage.get_messages()).where(function (signUpMessage) {
        return (signUpMessage.activity_name === Activity.find_running_activity().activity_name);
    })
};


SignUpMessage.get_messages_choosed = function () {
    return _(SignUpMessage.get_messages()).where(function (signUpMessage) {
        return (signUpMessage.activity_name === Activity.find_choosed_activity().activity_name);
    })
};
```

改过的代码

Javascript:

```
SignUpMessage.get_messages_activity_running = function (activity_name) {
    return _(SignUpMessage.get_messages()).where({.where({activity_name: Activity.find_running_activity.activity_name});
    })
};


SignUpMessage.get_messages_choosed = function () {
    return _(SignUpMessage.get_messdecayages()).findwhere({activity_name: Activity.find_running_activity().name});
};
```
5. 这个有问题的实例教会了你一个深刻的道理。 “牵一发动全身”这句话， 它一旦牵涉到程序就大不相同了。这个“动”这个坏事还需要你来做，呵呵呵……修改了一处的代码，因为逻辑改变，然后你还需要修改它别地的代码。

jade:

```
#wrappers.wrapper.scrollable
  ul.list-style-2
    li.clearfix.btn-default(ng-repeat='signUpMessage in signUpMessages track by $index')
      h3 {{signUpMessage.name}}
      i.p-left {{signUpMessage.phone}}
      span.glyphicon
```

改过的代码

jade:

```
#wrappers.wrapper.scrollable
  ul.list-style-2
    li.clearfix.btn-default(ng-repeat='signUpMessage in signUpMessages track by $index')
      h3 {{signUpMessage.message_name}}
      i.p-left {{signUpMessage.message_phone}}
      span.glyphicon
```

原因

在你以前的代码里

曾经用的是name 和 phone

而现在你用的是

message_name 和 message_phone

下面是你“牵”的那“一发”，就是你修改过的地方

以前的代码：

Javascript:

```
function SignUpMessage(register_name,phone, activity_name) {
    this.register_name = register_name;
    this.phone = phone;
    this.name = activity_name;
}
```

现在的代码

现在改了名字

以前的`register_name`， `phone`， `activity_name`

改成了`message_name`，`message_phone` ， `activity_name`

Javascript:

```
function SignUpMessage(message_name, message_phone, activity_name) {
    this.message_name = message_name;
    this.message_phone = message_phone;
    this.activity_name = activity_name;
}
```

你可别忘了，“全身”可不止这么一点，这里还有 ￼signUpMessages： [{"register_name":"XX人_1","message_phone":"18349131319","activity_name":"出差"}, {"register_name":"XX人_2","message_phone":"18349131319","activity_name":"出差"}, {"register_name":"XX人_3","message_phone":"18349131319","activity_name":"出差"}, {"register_name":"XX人_4","message_phone":"18349131319","activity_name":"出差"}, {"register_name":"XX人_5","message_phone":"18349131319","activity_name":"出差"}, {"register_name":"XX人_6","message_phone":"18349131319","activity_name":"出差"}, {"register_name":"XX人_7","message_phone":"18349131319","activity_name":"出差"}, {"register_name":"XX人_8","message_phone":"18349131319","activity_name":"出差"}, {"register_name":"XX人_9","message_phone":"18349131319","activity_name":"出差"}]
6. 这个实例只是用来教你，保存现场不及时，就是这个下场。呵呵呵……

Javascript:

```
    SignUpMessage.is_repeat = function () {


        return _(SignUpMessage.get_messages_activity_running()).some(function (signUpMessage) {


            return (signUpMessage.message_phone === message_phone) ;
        })
};
```

改过的代码

Javascript:

```
    SignUpMessage.is_repeat = function (message_phone) {


        return _(SignUpMessage.get_messages_activity_running()).some(function(signUpMessage) {


             return (signUpMessage.message_phone === message_phone);


    })
};
```
7. 这个实例教你，熟悉underscore要从熟悉它的函数参数做起。发现第一个函数需要参数

Javascript:

```
SignUpMessage.get_messages_activity_running = function () {
    return _(SignUpMessage.get_messages_choosed()).where(function () {
        return (signUpMessage.activity_name === Activity.find_running_activity().name);
    })
};
```

改过的代码

Javascript:

```
SignUpMessage.get_messages_activity_running = function () {
    return _(SignUpMessage.get_messages_choosed()).where(function (signUpMessage) {
        return (signUpMessage.activity_name === Activity.find_running_activity().name);
       })
};
```
8. 这个实例，问题看似不大，但他教你要时刻注意你需要的是什么，且看：`<get_messages_choosed()>` 和 `<get_messages()>`

javascript:

```
SignUpMessage.get_messages_choosed = function (activity_name) {
    return _(SignUpMessage.get_messages()).where({activity_name: activity_name});
};


SignUpMessage.get_messages_activity_running = function () {
    return _(SignUpMessage. get_messages_choosed).where({activity_name: Activity.find_running_activity().name})
};
```

修改后的代码

```
SignUpMessage.get_messages_choosed = function (activity_name) {
    return _(SignUpMessage.get_messages()).where({activity_name: activity_name});
};


SignUpMessage.get_messages_activity_running = function () {
    return _(SignUpMessage.get_messages()).where({activity_name: Activity.find_running_activity().name})
};
```

怎么样，Alfred，老师们讲得不错吧！今天又让你学到了不少知识吧！下一次还有更多新老师值得期待哦，下次再见！

August 4, 2014 10:25 PM
