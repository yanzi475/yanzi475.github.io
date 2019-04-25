 # js 常用方法/common  #

 在工作中我们的每一个项目中都有common，我们想当然会理解这是架构师会提炼出来的一些方法，可以，有时需要我们自己写的时候，你真的会吗？一下是我自己工作之余总结的需要自己必须掌握的。欢迎大家给补充，先谢过哈~~~

## 函数 ##

### 提取url参数###

页面之间的传递数据，除了缓存sessionStorage  || localStorage (自己根据实际情况自行选择)之外，还有一种特别普遍的方式。通过url中的参数传递。
第一种方法是获取url参数传递的跳转地址，这个是用encodeURIComponent编码，所以获取时要解码。第二个是常用的情景。

    function getQueryParam(name){
        var reg = new RegExp('(^|&)' + name + '=([^&]*)(&|$)', 'i');
        var r = window.location.search.substr(1).replace(/%20/g,"").match(reg);
        if(r) {
            return decodeURIComponent(r[2]);
        }
        return null;
    }
    
        /**
         * 查询URL参数
         * @param name
         * @param [url]
         * @returns {*}
         */
        getQueryString: function (name, url) {
            var reg = new RegExp('[&,?]' + name + '=([^\\&,\\#]*)', 'i');
            var value = reg.exec(url || location.href);
            return value ? value[1] : '';
        },


    
### 原生js封装ajax对象###

      var conf ={type:"get",
            url:"/omm/http/pss/queryFundCategory?fundCode="+fundCode,
            data:"fundCode="+fundCode,
            dataType:"json"};
        //创建xhr对象
        var createAjax = function() {
            var xhr = null;
            try {
                //IE系列浏览器
                xhr = new ActiveXObject("microsoft.xmlhttp");
            } catch (e1) {
                try {
                    //非IE浏览器
                    xhr = new XMLHttpRequest();
                } catch (e2) {
                    window.alert("您的浏览器不支持ajax，请更换！");
                }
            }
            return xhr;
        };
        
        // 初始化
        //type参数,可选
        var type = conf.type;
        //url参数，必填 
        var url = conf.url;
        //data参数可选，只有在post请求时需要
        var data = conf.data;
        //datatype参数可选    
        var dataType = conf.dataType;
        //回调函数可选
        var success = conf.success;
                                                                                            
        if (type == null){
            //type参数可选，默认为get
            type = "get";
        }
        if (dataType == null){
            //dataType参数可选，默认为text
            dataType = "text";
        }
        // 创建ajax引擎对象
        var xhr = createAjax();
        // 打开
        xhr.open(type, url, true);
        // 发送
        if (type == "GET" || type == "get") {
            xhr.send(null);
        } else if (type == "POST" || type == "post") {
            xhr.setRequestHeader("content-type",
                        "application/x-www-form-urlencoded");
            xhr.send(data);
        }
        xhr.onreadystatechange = function() {
            if (xhr.readyState == 4 && xhr.status == 200) {
                if(dataType == "text"||dataType=="TEXT") {
                    if (success != null){
                        //普通文本
                        success(xhr.responseText);
                    }
                }else if(dataType=="xml"||dataType=="XML") {
                    if (success != null){
                        //接收xml文档    
                        success(xhr.responseXML);
                    }  
                }else if(dataType=="json"||dataType=="JSON") {
                    if (success != null){
                        //将json字符串转换为js对象  
                        success(eval("("+xhr.responseText+")"));
                    }
                }
            }
        };

这个会在面试中出现，但确实也是基本功。函数申明后的}是没有分号的，而函数表达式后面要有分号，这是规范哈。其中一点定义conf变量，我们在项目中有时是从配置中读取的 var conf = sessionStorage.getItem("param") || {};后面加这个{}是很有意义的。这就要说到对象和变量的非法引用，哈哈~~~

### 正则表达式相关###

	/**
         * 常用正则表达式
         * @returns {boolean}
         */
        RegexMap : {
            //正负整数或浮点数
            intOrFloat: /^(-)?\d+(\.\d+)?$/,
            // 手机号码
            MobileNo: /^1[34587]\d{9}$/,
            // 银行卡号（大于或等于16位的数字）
            CardNo: /^\d{16,}$/,
            // 短验证码（6位数字以上）
            MobileCode: /^\d{6,}$/,
            // 交易密码(6-16位数字或字母)
            OrderPassword: /^\S{6,16}$/,
            //千分位正则
            parseThousands: /(\d{1,3})(?=(\d{3})+(?:$|\.))/g,
	     //千分位正则(二)
            parseThousands: /(\d)(?=(\d{3})+$)/g,    
            //每4位字符用空格隔开
            bankCardNo: /(\d{4})(?=\d)/g,
            //卡号屏蔽
            parseToStarNumber: /^(\d{4})(\d+)(\d{4})$/,
            // 后四位屏蔽
            parseRightFourStar: /^(\w+)(\w{4})$/,
            //日期格式检测
            parseDateFormat: /\b(\d{4})\b[^\d]+(\d{1,2})\b[^\d]+(\d{1,2})\b(\s(\d{1,2})\:(\d{1,2})\:(\d{1,2}))?[^\d]?/,
            // 出生日期掩码，显示格式（"19**年**月*2日")
            userBirthdayStarRegex: /(\d{2})\d{2}([^\d]+)\d+([^\d]+)\d?(\d)([^\d]+)?/
        },


### 字符串相关###

    function trim(text){
        return text.replace(/^\s+|\s+$/g,"");
    }
  //是否是微信环境
	IS_WECHAT: !!navigator.userAgent.match(/MicroMessenger/ig),
  //布尔转换字符（+号的一个用法）
   $scope.account.bindWechatFlag = +!$scope.account.bindWechatFlag;
   +true 为1，+false 为0， +!false为1.
   
   /**
   *  数字金额数加千位符
   * @param number
   */
   vartoThousands = function(number) {
	    return (number + '').replace(/(\d)(?=(\d{3})+$)/g, '$1,');
	}
   
   ```
     /**
   * 逆序单词
   * @param word
   */
  function reverseWord (word) {
    if (!word) return
    if (word.indexof(' ') < 0) return word
    let arr = word.split(' ').reverse()
    return arr.join(' ')
  }

  /**
   * 输出本字符串中出现最多的字符和次数
   * @param str
   */
  function findMax (str) {
    if (!str) return
    let obj = {}
    for (var i = 0; i < str.length; i++) {
      let item = str.charAt(i)
      if (obj[item]) {
        obj[item]++
      } else {
        obj[item] = 1
      }
    }
    let max = 0
    let maxKey = ''
    for (let key in obj) {
      if (max <  obj[key]) {
        max = obj[key]
        maxKey = key
      }
    }
    return {
      max: max,
      maxKey: maxKey
    }
  }
  /**
   * 输出本字符串中只出现一次并且最靠前的那个字符的位置
   * @param str
   */
  function findOne (str) {
    if (!str) return
    let obj = {}
    for (var i = 0; i < str.length; i++) {
      let item = str.charAt(i)
      if (obj[item]) {
        obj[item]++
      } else {
        obj[item] = 1
      }
    }
    let position = 0
    let item = ''
    for (let key in obj) {
      if (obj[key] === 1) {
        position = obj[key]
        item = key
        break
      }
    }
    return {
      position: position,
      item: item
    }
  }
  /**
   *
   * @param str
   */
  function findOneStr (str) {
    if (!str) return
    let obj = {}
    var arr = str.split('')
    var arrNew = Array.from(new Set(arr))
    let position = str.indexOf(arrNew[0])
    let item = arrNew[0]
    for (let key of arrNew) {
      if (position > str.indexOf(key)) {
        position = str.indexOf(key)
        item = key
      }
    }
    return {
      position: position,
      item: item
    }
  }
   
   ```
   
###大写金额的一些转换####

       /**
         * 将数字转化为大写汉字
         * @param num 待转换的数字
         * @param piece 转换的单位（默认为元）
         * @param nocomma 不要小数后的数字，默认为undefined(false)
         * @returns {string}
         */
        chineseNum: function (num, piece, nocomma) {
            if(!piece){
                piece = '元';
            }

            if (!/^\d*(\.\d*)?$/.test(num)) { return ""; }
            var AA = ["零", "壹", "贰", "叁", "肆", "伍", "陆", "柒", "捌", "玖"];
            var BB = ["", "拾", "佰", "仟", "萬", "億", "点", ""];
            var a = ("" + num).replace(/(^0*)/g, "").split("."), k = 0, re = "";
            for (var i = a[0].length - 1; i >= 0; i--) {
                switch (k) {
                    case 0: re = BB[7] + re; break;
                    case 4: if (!new RegExp("0{4}\\d{" + (a[0].length - i - 1) + "}$").test(a[0]))
                        re = BB[4] + re; break;
                    case 8: re = BB[5] + re; BB[7] = BB[5]; k = 0; break;
                }
                if (k % 4 == 2 && a[0].charAt(i + 2) != 0 && a[0].charAt(i + 1) == 0) re = AA[0] + re;
                if (a[0].charAt(i) != 0) re = AA[a[0].charAt(i)] + BB[k % 4] + re; k++;
            }

            if(!re){
                //前面为空，则统称零
                re = AA[0]
            }

            if(nocomma){
                return re + (!!re?piece:'');
            }

            if (a.length > 1 && a[1] && piece == '元'  ) //加上小数部分(如果有小数部分)
            {
                //如果小数点前面没有值，说明没有元，不需要加上单位元
                if(re!=='')re += piece;

                if(a[1].charAt(0)!='0'||(a[1].charAt(1)==''?'0':a[1].charAt(1))!='0') re += AA[a[1].charAt(0)] + '角';
                if(a[1].charAt(1)&&a[1].charAt(1)!='0') re += AA[a[1].charAt(1)] + '分';
                return re;
            }

            else if(a.length > 1 && a[1]){

                if(a[1].charAt(0)!='0'||(a[1].charAt(1)==''?'0':a[1].charAt(1))!='0') re += (BB[6] + AA[a[1].charAt(0)]);
                if(a[1].charAt(1)&&a[1].charAt(1)!='0') re += AA[a[1].charAt(1)];
                if(re!=='')re += piece;
                return re;
            }

            return re + piece;
        }
        
 ###字符添加千分位####
  一些购买产品的数字金额，都会有添加数字分隔符的需求，当然angular中有input-number 指令来规范了。最原始的方法是查询字符加分隔符，正则表达式的方式是最简单的，如下：
  
          
        function(n) {
            var re = /\d{1,3}(?=(\d{3})+$)/g;
            var n1 = n;
            if (n) {
                n1 = n.replace(/^(\d+)((\.\d+)?)$/, function(s, s1, s2) {
                    return s1.replace(re, "$&,") + s2;
                });
            }
            return n1;
        }
	
###日期的处理####
```
   /**
   * 在initDate格式（YYYY-MM-DD）上增加指定日期的函数
   * @param interval
   * @param number
   * @param setDate
   * @returns {*}
   * @constructor DateAdd( "D", 5, minDate);
   * DateAdd(interval,number,date)
   */
  static DateAdd(interval, number, initDate) {
    let date = new Date(initDate)
    switch (interval) {
      case 'Y': {
        date.setFullYear(date.getFullYear() + number)
        return date
      }
      case 'M': {
        date.setMonth(date.getMonth() + number)
        return date
      }

      case 'D': {
        date.setDate(date.getDate() + number)
        return date
      }
      case 'h' : {
        date.setHours(date.getHours() + number)
        return date
      }
      case 'm': {
        date.setMinutes(date.getMinutes() + number)
        return date
      }
      case 's' : {
        date.setSeconds(date.getSeconds() + number)
        return date
      }
      default: {
        date.setDate(date.getDate() + number)
        return date
      }
    }
  }

  /**
   * 打印一段时间间隔
   * @param start   // 快捷键的是 /** 回车
   * @param end
   * @param gap
   */
  static printTime(str, gap) {
    // printTime
    let arr = str.split('-')
    var startTime = new Date('2018-06-06T' + arr[0])
    var endTime = new Date('2018-06-06T' + arr[1])
    let time = startTime
    var list = []
    let flag = true
    while (flag) {
      var firstH = time.getHours() < 10 ? `0${time.getHours()}` : time.getHours()
      var firstM = time.getMinutes()
      time.setMinutes(firstM + gap)
      firstM = firstM < 10 ? `0${firstM}` : firstM
      var objF = firstH + ':' + firstM
      var secondH
      var secondM
      var objS, obj
      if (time < endTime) {
        secondH = time.getHours()
        secondM = time.getMinutes()
        secondH = secondH < 10 ? `0${secondH}` : secondH
        secondM = secondM < 10 ? `0${secondM}` : secondM
        objS = secondH + ':' + secondM
      } else {
        flag = false
        secondH = endTime.getHours()
        secondM = endTime.getMinutes()
        secondH = secondH < 10 ? `0${secondH}` : secondH
        secondM = secondM < 10 ? `0${secondM}` : secondM
        objS = secondH + ':' + secondM
      }
      obj = objF + '~' + objS
      list.push(obj)
    }
    return list
  }

  /**
   * 日期的合法校验
   * @param date
   * @returns {boolean}
   */
  static isValidDate(date) {
    return date instanceof Date && !isNaN(date.valueOf())
  }
  /**
   * 判断当前时间是否在某个时间段内
   * @param nowDate
   * @param minDate
   * @param maxDate
   * @returns {boolean}
   */
  static checkDateRange(nowDate, minDate, maxDate) {
    if (this.isValidDate(nowDate) && this.isValidDate(minDate) && this.isValidDate(maxDate)) {
      return nowDate.getTime() >= minDate.getTime() && nowDate.getTime() <= maxDate.getTime()
    } else {
      return false
    }
  }
  static checkAge(birthday, minAge, maxAge) {
    let today = new Date()
    birthday = birthday + ' 00:00:00'
    birthday = new Date(birthday)
    birthday.setFullYear(birthday.getFullYear() + minAge)
    return today >= birthday
  }
  static checkNumber(number) {
    let reg = /^\d+$/
    return reg.test(number)
  }
        
 ```  
 
##### 身份证校验
```
class StringUtils {
  constructor() {
    /**
     * 函数节流
     *
     * 待续
     */
    this.Reg = {
      regList: {
        // phone: /^0?(13[0-9]|15[012356789]|17[678]|18[0-9]|19[0-9]|14[57])[0-9]{8}$/i,
        phone: /^1[345789]\d{9}$/,
        name: /^[\u4e00-\u9fa5]{2,32}$/,
        firstName: /^([\u4e00-\u9fa5]){1,16}$/,
        familyName: /^([\u4e00-\u9fa5]){1,16}$/,
        ident: /(^\d{15}$)|(^\d{18}$)|(^\d{17}(\d|X|x)$)/i,
        pwd: /^[0-9]{6}$/,
        otp: /^[0-9]{6}$/,
        address: /^([\u4e00-\u9fa5]){2,64}$/,
        money: /^\d+(\.\d{1,2})?$/,
        zip: /^[0-9]{6}$/,
        bankcard: /^([1-9]{1})(\d{14}|\d{18})$/,
        // email: /^([a-zA-Z0-9_.-])+@(([a-zA-Z0-9-])+\.)+([a-zA-Z0-9]{2,16})+$/
        email: /^\w+((-\w+)|(\.\w+))*@[A-Za-z0-9]+((\.|-)[A-Za-z0-9]+)*\.[A-Za-z0-9]+$/
      },
      test: function (value, name) {
        if (!this.regList[name] || !value) {
          return false
        } else {
          return this.regList[name].test(value)
        }
      }
    }
  }
  /**
   * 输入过程中最大位数的实现
   */
  static isMaxNum(str, max) {
    let res
    if (!str || !max) {
      res = ''
      return res
    }
    if (str.length >= max) {
      res = str.substring(0, max)
    } else {
      res = str
    }
    return res
  }
  /**
   * 身份证最后校验位的判断
   * @param ID
   * @returns {boolean}
   */
  static validLastId (ID) {
    var arrInt = [7, 9, 10, 5, 8, 4, 2, 1, 6, 3, 7, 9, 10, 5, 8, 4, 2]
    var arrCh = ['1', '0', 'X', '9', '8', '7', '6', '5', '4', '3', '2']
    var sum = 0
    var residue
    if (ID.toString().length !== 18) {
      return false
    }
    for (var i = 0; i < 17; i++) {
      sum += ID.substr(i, 1) * arrInt[i]
    }
    residue = arrCh[sum % 11]
    if (residue === ID.substr(17, 1)) {
      return true
    } else {
      return false
    }
  }
  /**
   * 性别校验
   */
  static validIdSex(str) {
    var flag
    if (str && str.length === 18) {
      flag = str.toString().charAt(16)
      if (flag % 2 === 0) {
        return '女'
      } else {
        return '男'
      }
    } else if (str && str.length === 15) {
      flag = str.toString().charAt(14)
      if (flag % 2 === 0) {
        return '女'
      } else {
        return '男'
      }
    }
  }
  /**
   * 中文姓名正则的校验
   */
  static chineseName(str) {
    let chineseReg = /^[\u4E00-\u9FA5\uf900-\ufa2d·s]{2,20}$/
    return chineseReg.test(str)
  }
  
  export {StringUtils}
```
  
	
