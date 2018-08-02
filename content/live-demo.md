+++
menu = "main"
title = "live demo"
weight = 7
+++

<html lang="en">
<head>
  <title>Zaal</title>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css">
  <script src="https://ajax.googleapis.com/ajax/libs/jquery/3.3.1/jquery.min.js"></script>
  <script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/js/bootstrap.min.js"></script>
</head>
<body>

<script type="text/javascript" language="javascript">

function sortByKey(array, key) {
return array.sort(function(a, b) {
    var x = a[key]; var y = b[key];
    return ((x > y) ? -1 : ((x < y) ? 1 : 0));
});
}

function userActionProfanityDetector() {
    var xhttp = new XMLHttpRequest();
    xhttp.open("POST", "http://88.99.203.40:9691/Zaal/api", false);
    xhttp.setRequestHeader("Content-type", "application/json");
    xhttp.setRequestHeader('Access-Control-Allow-Origin', '*');
    var doc = document.getElementById("document_pda").value.replace(/[\n"']/g, "");
    var load = `{"method": "profanity_detection", "args":{"document":"${doc}"}}`;
    xhttp.send(load);
    if (xhttp.status == 200) {
        var obj = JSON.parse(xhttp.responseText);
        document.getElementById("score_pd").value = obj.results.pay_load.profanity_score
    } else if (xhttp.status == 401) {
          alert(xhttp.responseText)
    }
    else {
        alert("No connection to server ...")
    }
}


function userActionDSA() {
    var xhttp = new XMLHttpRequest();
    xhttp.open("POST", "http://88.99.203.40:9691/Zaal/api", false);
    xhttp.setRequestHeader("Content-type", "application/json");
    xhttp.setRequestHeader('Access-Control-Allow-Origin', '*');
    var doc = document.getElementById("document_dsa").value.replace(/[\n"']/g, "");
    var load = `{"method": "sentiment_analyzer", "args":{"document":"${doc}"}}`;
    xhttp.send(load);
    if (xhttp.status == 200) {
        var obj = JSON.parse(xhttp.responseText);
        document.getElementById("score_dsa").value = obj.results.pay_load.sentiment_score
    } else if (xhttp.status == 401) {
          alert(xhttp.responseText)
    }
    else {
        alert("No connection to server ...")
    }
}

function userActionASA() {
    var xhttp = new XMLHttpRequest();
    xhttp.open("POST", "http://88.99.203.40:9691/Zaal/api", false);
    xhttp.setRequestHeader("Content-type", "application/json");
    xhttp.setRequestHeader('Access-Control-Allow-Origin', '*');
    var doc = document.getElementById("document_asa").value.replace(/[\n"']/g, "");
    var aspect = document.getElementById("aspect").value.replace(/[\n"']/g, "");
    
    var load = `{"method": "aspect_sentiment_analyzer", "args":{"document": "${doc}", "aspect": "${aspect}"}}`;
    
    xhttp.send(load);
    if (xhttp.status == 200) {
        var obj = JSON.parse(xhttp.responseText);
        document.getElementById("score_asa").value = obj.results.pay_load.sentiment_score
    } else if (xhttp.status == 401) {
          alert(xhttp.responseText)
    }
    else {
        alert("An error occurred ...")
    }
}


function json2table(json, classes) {
  var cols = Object.keys(json[0]);
  var headerRow = '';
  var bodyRows = '';
  classes = classes || '';
  cols.map(function(col) {
  headerRow += '<th>' + col + '</th>';
});

json.map(function(row) {
  bodyRows += '<tr>';

  cols.map(function(colName) {
  bodyRows += '<td>' + row[colName] + '</td>';
});

  bodyRows += '</tr>';
});

return '<table class="table table-striped text-center" style="display:inline; width:300px; text-align:center" ><thead><tr>' +
       headerRow +
       '</tr></thead><tbody>' +
       bodyRows +
       '</tbody></table>';
}

function userActionDC() {
    var xhttp = new XMLHttpRequest();
    xhttp.open("POST", "http://88.99.203.40:9691/Zaal/api", false);
    xhttp.setRequestHeader("Content-type", "application/json");
    xhttp.setRequestHeader('Access-Control-Allow-Origin', '*');
    var doc = document.getElementById("document_dc").value.replace(/[\n"']/g, "");
    var load = `{"method": "document_classifier", "args":{"document":"${doc}"}}`;
    xhttp.send(load);
    if (xhttp.status == 200) {
        var data = []
        var responseJson = JSON.parse(xhttp.responseText);
      
        for (var key in responseJson.results.pay_load) {
                if (data.indexOf(key) === -1) {
                    data.push({"class": key, "class_weight": responseJson.results.pay_load[key]});
                }
            }
      sortByKey(data, "class_weight")
      document.getElementById('classTable').innerHTML = json2table(data);
    } else if (xhttp.status == 401) {
          alert(xhttp.responseText)
    }
    else {
        alert("No connection to server ...")
    }
}

String.prototype.allReplace = function(obj) {
    var retStr = this;
    for (var x in obj) {
        retStr = retStr.replace(new RegExp(x, 'g'), obj[x]);
    }
    return retStr;
};

function isBlank(str) {
    return (!str || /^\s*$/.test(str));
}

function userActionEE() {
    var xhttp = new XMLHttpRequest();
    xhttp.open("POST", "http://88.99.203.40:9691/Zaal/api", false);
    xhttp.setRequestHeader("Content-type", "application/json");
    xhttp.setRequestHeader('Access-Control-Allow-Origin', '*');
    var word = document.getElementById("word").value.replace(/[\n"']/g, "");
    var k = document.getElementById("k").value.allReplace({"۰": "0", "۱": "1", "۲": "2", "۳": "3", "۴": "4", "۵": "5", "۶": "6", "۷": "7", "۸": "8", "۹": "9"})
    
    if (isBlank(k)){
    k = 1
    }
    var load = `{"method": "word_suggester", "args":{"word": "${word}", "k-nearest": ${k}}}`;
    
    xhttp.send(load);
    if (xhttp.status == 200) {
        var data = []
        var responseJson = JSON.parse(xhttp.responseText);
        for (var key in responseJson.results.pay_load) {
                if (data.indexOf(key) === -1) {
                    data.push({"word": key, "simlarity_score": responseJson.results.pay_load[key]});
                }
            }
      sortByKey(data, "simlarity_score")
      document.getElementById('classTableEE').innerHTML = json2table(data);
    } else if (xhttp.status == 401) {
          alert(xhttp.responseText)
    }
    else {
        alert("An error occurred ...")
    }
}

function userActionKWE() {
    var xhttp = new XMLHttpRequest();
    xhttp.open("POST", "http://88.99.203.40:9691/Zaal/api", false);
    xhttp.setRequestHeader("Content-type", "application/json");
    xhttp.setRequestHeader('Access-Control-Allow-Origin', '*');
    var doc = document.getElementById("document_kwe").value.replace(/[\n"']/g, "");
    var load = `{"method": "keyword_extractor", "args":{"document":"${doc}"}}`;
    xhttp.send(load);
    if (xhttp.status == 200) {
        var data = []
        var responseJson = JSON.parse(xhttp.responseText);
        for (var key in responseJson.results.pay_load) {
                if (data.indexOf(key) === -1) {
                    data.push({"word": key, "word_weight": responseJson.results.pay_load[key]});
                }
            }
        
      sortByKey(data, "word_weight")
      document.getElementById('classTableKWE').innerHTML = json2table(data);
    } else if (xhttp.status == 401) {
          alert(xhttp.responseText)
    }
    else {
        alert("No connection to server ...")
    }
}

</script>

<div class="jumbotron text-center">
  <h1>Zaal</h1>
  <p>NLP toolkit as service for Persian language</p> 
</div>


<div class="container">
       <h2>Profanity Detection: </h2>
       <form>
         <div class="form-group">
            <textarea class="form-control" placeholder="مرتیکه ... انقدر چرت نگو" rows="5" dir="rtl" id="document_pda" name="document" style='display:inline; width:680px;'></textarea>
        </div> 
       </form>
</div>

<div class="submitbutton text-center" style="margin-top:10px">
<button onclick="userActionProfanityDetector()" type="button" class="btn btn-success">process</button>
</div>

<div class="myscore_psore text-center" style="margin-top:20px">
<input type="text" placeholder="Profanity Score" name="name" id="score_pd" class="form-control"  style='display:inline; width:150px; text-align:center'/>
</div>


<div class="container">
       <h2>Document Level Sentiment Analysis: </h2>
       <form>
         <div class="form-group">
            <textarea class="form-control" placeholder="به نظرم ارزش خریدشو داشت!" rows="5" dir="rtl" id="document_dsa" name="document" style='display:inline; width:680px;'></textarea>
        </div> 
       </form>
</div>

<div class="submitbutton text-center" style="margin-top:10px">
<button onclick="userActionDSA()" type="button" class="btn btn-success">process</button>
</div>

<div class="myscore_dsa text-center" style="margin-top:20px">
<input type="text" placeholder="Sentiment Score" name="name" id="score_dsa" class="form-control"  style='display:inline; width:150px; text-align:center'/>
</div>


<div class="container">
         <h2>Aspect Based Sentiment Analysis: </h2>
       <form>
        <div class="form-group">
            <textarea class="form-control" placeholder="به نظرم دوربین خوبیه" rows="5" dir="rtl" id="document_asa" name="document" style='display:inline; width:680px;'></textarea>
        </div> 
        <div class="input-group" style="margin-top:10px">
        <span class="input-group-addon">aspect</span>
            <input dir="rtl" id="aspect" type="text" class="form-control" placeholder="دوربین"  name="document" style='display:inline; text-align:center; width:612px;'>
        </div>
       </form>
</div>

<div class="submitbutton text-center" style="margin-top:10px">
<button onclick="userActionASA()" type="button" class="btn btn-success">process</button>
</div>

<div class="myscore_asa text-center" style="margin-top:20px">
<input type="text" placeholder="Sentiment Score" name="name" id="score_asa" class="form-control"  style='display:inline; width:150px; text-align:center'/>
</div>


<div class="container">
      <h2>Document Classification:</h2>
       <form>
        <div class="form-group">
            <textarea class="form-control" placeholder="به گزارش خبرنگار قضایی فارس، سعید مرتضوی دادستان سابق تهران که به دو سال حبس قطعی محکوم شده است، روز یک‌شنبه (1397/2/2) توسط مأمورین پلیس دستگیر و بعد از ظهر همان روز جهت تحمل محکومیت حبس به زندان اوین تحویل گردید." rows="5" dir="rtl" id="document_dc" name="document" style='display:inline; width:680px;'></textarea>
        </div>
       </form>
</div>

<div class="submitbutton text-center" style="margin-top:30px">
<button onclick="userActionDC()" type="button" class="btn btn-success">process</button>
</div>

<div id="classTable" class="dc_table text-center" style="margin-top:30px;" ></div>

<div class="container">
      <h2>Word Embedding:</h2>
       <form>
        <div class="input-group">
            <span class="input-group-addon">word</span>
            <input dir="rtl" id="word" type="text" class="form-control" name="document" placeholder="تبلت" style='display:inline; text-align:center; width:630px;'>
        </div>
        <div class="input-group" style="margin-top:10px">
        <span class="input-group-addon">count</span>
            <input dir="rtl" id="k" type="text" class="form-control" name="document" placeholder="5" style='display:inline; text-align:center; width:630px;'>
        </div>
       </form>
</div>

<div class="submitbutton text-center" style="margin-top:30px">
<button onclick="userActionEE()" type="button" class="btn btn-success">process</button>
</div>

<div id="classTableEE" class="simlarity_table text-center" style="margin-top:30px;" ></div>


<div class="container">
      <h2>Keyword Extraction:</h2>
       <form>
        <div class="form-group">
            <textarea class="form-control" placeholder="به گزارش گروه بین‌الملل خبرگزاری فارس، دیپلمات‌های مطلع در آستانه سفر رسمی روز دوشنبه «امانوئل ماکرون» رئیس جمهور فرانسه به آمریکا و دیدار با «دونالد ترامپ» رئیس جمهور آمریکا، به کار سخت متقاعد کردن رئیس جمهور آمریکا برای ماندن در برجام اشاره کردند."  rows="5" dir="rtl" id="document_kwe" name="document" style='display:inline; width:680px;'></textarea>
        </div>
       </form>
</div>

<div class="submitbutton text-center" style="margin-top:30px">
<button onclick="userActionKWE()" type="button" class="btn btn-success">process</button>
</div>

<div id="classTableKWE" class="kweTable text-center" style="margin-top:30px;" ></div>

</body>
</html>
