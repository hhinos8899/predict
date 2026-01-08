<!doctype html>
<html lang="zh-CN">
<head>
<meta charset="utf-8">
<title>DeepSeek + ChatGPT 风控决策器（本地版）</title>
<style>
body{font-family:system-ui;background:#0b0c0f;color:#e9eef8;padding:20px}
.box{border:1px solid #333;border-radius:12px;padding:16px;margin-bottom:12px}
input,textarea,button{
  background:#111;color:#eee;border:1px solid #333;
  border-radius:8px;padding:8px;font-size:14px
}
button{cursor:pointer;font-weight:700}
.big{font-size:28px;font-weight:900;padding:14px;border-radius:12px;text-align:center}
.ok{background:#123;color:#2ee08a}
.no{background:#200;color:#ff6b6b}
.small{color:#aaa;font-size:13px}
</style>
</head>
<body>

<h2>方案 3 · DeepSeek 出方向 + 风控裁判</h2>

<div class="box">
  <div class="small">历史（只写 B / P）：</div>
  <input id="history" style="width:100%" placeholder="BPBBPBPB">
</div>

<div class="box">
  <div class="small">DeepSeek 输出（粘贴 JSON）：</div>
  <textarea id="deepseek" rows="4" style="width:100%"
placeholder='{"direction":"B","confidence":0.72}'></textarea>
</div>

<div class="box">
  <label><input type="checkbox" id="used"> 本结构已下注过</label>
</div>

<button onclick="run()">执行判断</button>

<div class="box">
  <div id="result" class="big no">等待输入</div>
  <div id="reason" class="small"></div>
</div>

<script>
function last6(str){
  return str.replace(/[^BP]/g,'').slice(-6);
}

function run(){
  const history=document.getElementById("history").value;
  let ds;
  try{
    ds=JSON.parse(document.getElementById("deepseek").value);
  }catch(e){
    alert("DeepSeek JSON 格式不对");
    return;
  }
  const used=document.getElementById("used").checked;
  const recent6=last6(history);

  const result=document.getElementById("result");
  const reason=document.getElementById("reason");

  if(recent6.length<6){
    result.textContent="不要动";
    result.className="big no";
    reason.textContent="数据不足";
    return;
  }

  if(ds.direction==="WAIT" || ds.confidence<0.6){
    result.textContent="不要动";
    result.className="big no";
    reason.textContent="方向不明确";
    return;
  }

  if(used){
    result.textContent="不要动";
    result.className="big no";
    reason.textContent="本结构已用过一次";
    return;
  }

  result.textContent="可以动，下注 "+ds.direction;
  result.className="big ok";
  reason.textContent="recent6="+recent6;
}
</script>
</body>
</html>
