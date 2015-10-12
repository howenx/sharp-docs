# style-imgprocess API接口文档

**作者：熊浩**

**日期：2015/10/07**

**版本：v0.0.1**

## 目录

[1. 上传、压缩图片接口](#upload)</br>
[2. 请求缩略图接口](#thumb)</br>
[3. 分割图片接口](#split)</br>
[4. 模板截图接口](#shot)

## <a name="upload"></a>1. 上传，压缩图片接口

#### 接口功能
> 上传图片到测试服务器

#### 接口说明
> 图片类型仅仅支持JPEG,PNG,GIF,WEBP格式，并只提供JPEG和PNG格式的压缩，其余格式都是原图存储

#### 请求地址
> [http://172.28.3.51:3000/upload](http://172.28.3.51:3000/upload)

#### 支持格式
> JSON

#### HTTP请求方式
> POST

#### 请求参数
> |参数|必选|类型|说明|
|:-----  |:-------|:-----|-----                               |
|displayImage    |ture    |file|文件名                          |
|params    |true    |string   |压缩标志，'minify'：压缩；'unminify'：不压缩 |

#### 返回字段
> |返回字段|字段类型|说明                              |
|:-----   |:------|:-----------------------------   |
|error    | string    |返回错误码，详见[错误码解释](#errorcode)   |
|message  |string |具体错误内容                      |
|imgid |string |图片名称+后缀                        |
|compress |string |图片压缩处理结果数据 1.error: 处理结果状态 2.before:图片压缩前大小 3.after: 图片处理后大小 4.rate: 压缩率 5.time: 压缩用时                       |
|minify_url |string |返回压缩后图片请求地址                        |

#### 请求示例
> ```
var formdata = new FormData();
formdata.append("photo", file);
formdata.append("params", params);
var http = new XMLHttpRequest();
var url = "http://172.28.3.18:3008/upload";
http.open("POST", url, true);
http.onreadystatechange = function() {
	if (http.readyState == 4 && http.status == 200) {
		var data = JSON.parse(http.responseText);
		alert(data.message);
		if (typeof data.compress != 'undefined' && data.compress != null) {
			$('#gpicnm').append('<span style="display:block;margin:10px;width:100%;">第' + ($('#gallery').children().length) + '张图片名称：<b>' + data.imgid + '</b><br><b>压缩前大小:' + data.compress.before + ' 压缩后大小:' + data.compress.after + ' 用时:' + data.compress.time + ' 压缩率:' + data.compress.rate + '</b></span>');
		}
		$(':radio[name=select-minify]').each(function(index, element) {
			$(this).prop('checked', false);
		})
	}
}
http.send(formdata);
```

#### 响应示例
> ```
{
    "compress": {
        "after": "0.14M",
        "before": "1.12M",
        "error": "ok",
        "rate": "87.40%",
        "time": "0mm0ss"
    },
    "error": "000",
    "imgid": "8fccbf40402810fdee2d8b84b2e7b56d.jpg",
    "message": "Image Uploaded.",
    "minify_url": "http://192.168.31.103:3002/uploads/minify/8fccbf40402810fdee2d8b84b2e7b56d.jpg"
}
```

## <a name="thumb"></a>2. 请求缩略图接口

#### 接口功能
> 请求图片缩略图

#### 接口说明
> 根据请求后缀直接等比缩放，并以请求的宽度的缩放比来缩放高度

#### 请求地址
> [http://172.28.3.51:3000/thumb/原图名称_宽×高.jpg](http://172.28.3.51:3000)
>>URL示例：
```http://172.28.3.51:3002/thumb/47ddf9b7c38a1debc3f3bfb1b93b4ed7.jpg_750×600.jpg```

#### 支持格式
> JSON

#### HTTP请求方式
> GET

#### 请求参数
> 使用RESTful请求方式： ```http://172.28.3.51:3002/thumb/:id```<br>
> 直接返回缩略图的请求方式： ```http://172.28.3.51:3002/thumb/file/:id```

#### 返回字段
> |返回字段|字段类型|说明                              |
|:-----   |:------|:-----------------------------   |
|error    | string    |返回错误码，详见[错误码解释](#errorcode)   |
|message  |string |具体错误内容                      |
|thumb_url |string |返回缩略图片请求地址 |

#### 请求示例
> 见请求参数解释

#### 响应示例
> ```
{
    "error": "000",
    "message": "ok.",
    "thumb_url": "http://172.28.3.51:3002/uploads/thumb/8fd5f94983140081e30aece7d534f33d.jpg_750\u00d7600.jpg"
}
```

## <a name="split"></a>3. 分割图片接口

#### 接口功能
> 将整张图片横切成三份

#### 接口说明
> 根据请求传递的图片名称参数将其分割三份，并返回三份图片的URL

#### 请求地址
> [http://172.28.3.51:3000/split/file/:id](http://172.28.3.51:3000)

#### 支持格式
> JSON

#### HTTP请求方式
> GET

#### 请求参数
> 使用RESTful请求方式： ```http://172.28.3.51:3002/split/file/:id```<br>

#### 返回字段
> |返回字段|字段类型|说明                              |
|:-----   |:------|:-----------------------------   |
|error    | string    |返回错误码，详见[错误码解释](#errorcode)   |
|message  |string |具体错误内容                      |
|split_url |array |返回分割后的三张图片请求地址 |

#### 请求示例
> 见请求参数解释

#### 响应示例
> ```
{
    "error": "000",
    "message": "ok.",
    "split_img": "[\"http://192.168.31.103:3002/uploads/split/5d70f574ed5b4bcca0be125b9ff5d600.jpg\",\"http://192.168.31.103:3002/uploads/split/46f7ddb17a8b4f6b83601b24b046106a.jpg\",\"http://192.168.31.103:3002/uploads/split/ae52a5bc38f840ee9810bbfb374393c5.jpg\"]"
}
```

## <a name="shot"></a>4. 模板截图接口

#### 接口功能
> 指定模板截图

#### 接口说明
> 根据预先做好的HTML模板，通过传参指定ID来获取对应的模板的截图URL

#### 请求地址
> [http://172.28.3.51:3000/shotcut/:tempid](http://172.28.3.51:3000)

#### 支持格式
> JSON

#### HTTP请求方式
> GET

#### 请求参数
> 使用RESTful请求方式： ```http://172.28.3.51:3000/shotcut/:tempid```<br>

#### 返回字段
> |返回字段|字段类型|说明                              |
|:-----   |:------|:-----------------------------   |
|error    | string    |返回错误码，详见[错误码解释](#errorcode)   |
|message  |string |具体错误内容                      |
|shot_url | string |返回截取后图片的请求地址 |

#### 请求示例
> 见请求参数解释

#### 响应示例
> ```
{
    "error": "000",
    "message": "ok.",
    "shot_url": "http://192.168.31.103:3002/uploads/shot/e648d6f5ca304989bae1e6ad34e21ec0.png"
}
```

#### <a name="errorcode"></a>错误码解释
>|错误码|错误描述|解决方案                              |
|:-----   |:------|:-----------------------------   |
|000    |处理成功    |   |
|444    |传输错误    |   |
|445    |参数错误或者请求不合要求    | 具体参见返回的message内容进行相应修正  |