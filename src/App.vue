<template>
<div class="app">

  <!-- ===== LOGIN ===== -->
  <div v-if="pg==='login'" class="pg">
    <div class="hero"><div class="hero-bg"></div><div class="logo">C</div><h1>CLOUDTACTICS</h1><p class="sub">AI 实时对局分析引擎</p></div>
    <div class="card login-card">
      <input class="inp" placeholder="手机号" v-model="ph">
      <input class="inp" type="password" placeholder="密码" v-model="pw">
      <div class="err" v-if="err">手机号或密码错误</div>
      <button class="btn" @click="login">登录</button>
      <button class="btn-out" @click="login">注册</button>
    </div>
    <div class="ft-grid">
      <div class="ft" v-for="(f,i) in feats" :key="i"><span class="num">{{String(i+1).padStart(2,'0')}}</span><b>{{f.t}}</b><small>{{f.d}}</small></div>
    </div>
  </div>

  <!-- ===== HOME ===== -->
  <div v-else-if="pg==='home'" class="pg">
    <div class="tb"><span class="t1">Hello Summoner</span><span>⚙ 🔔</span></div>
    <div class="card pc"><span class="rk">GOLD IV</span><div class="st"><span>胜率 58%</span><span>KDA 3.8</span></div></div>
    <div class="sec">AI 推荐</div>
    <div class="row"><div class="mi" v-for="c in heroes" :key="c" @click="pg='match'">{{c}}</div></div>
    <div class="sec">最近对局</div>
    <div class="mh win" @click="pg='match'">Aatrox 12/3/8</div><div class="mh lose">Jinx 4/7/2</div><div class="mh win">Yasuo 9/1/6</div>
    <div class="sec">阵容推荐</div>
    <div class="row"><div class="mi" v-for="c in ['运营小法','重装龙王','幻灵皎月']" :key="c" @click="pg='comps'">{{c}}</div></div>
  </div>

  <!-- ===== MATCH DETAIL ===== -->
  <div v-else-if="pg==='match'" class="pg">
    <div class="tb"><span @click="pg='home'" class="back">← 返回</span><span>Match Detail</span><span></span></div>
    <div class="card tc"><div class="hn">AATROX</div><div class="rs win">VICTORY</div><div class="kd"><div>K 12</div><div>D 3</div><div>A 8</div></div><div class="ks">KDA 6.7</div></div>
    <div class="card"><div class="sec">Performance</div>
      <div class="br"><span>Impact</span><div class="pr"><div class="fl" style="width:82%"></div></div></div>
      <div class="br"><span>Team Fight</span><div class="pr"><div class="fl" style="width:76%"></div></div></div>
      <div class="br"><span>Survivability</span><div class="pr"><div class="fl" style="width:61%"></div></div></div>
    </div>
    <div class="card"><div class="sec">AI 复盘</div><div class="ai">✔ 前期成功2次gank建立优势</div><div class="ai">⚠ 中期站位略激进导致阵亡</div><div class="ai">✔ 团战输出贡献高</div></div>
    <div class="card"><div class="sec">时间轴</div>
      <div class="tl win"><span class="tm">02:10</span>First Blood</div>
      <div class="tl obj"><span class="tm">05:30</span>Dragon Fight</div>
      <div class="tl win"><span class="tm">08:45</span>Kill+Assist</div>
      <div class="tl lose"><span class="tm">12:10</span>Death</div>
    </div>
  </div>

  <!-- ===== COMPS ===== -->
  <div v-else-if="pg==='comps'" class="pg">
    <div class="tb"><span class="t1">T0阵容排行榜</span></div>
    <div class="card" v-for="c in comps" :key="c.n" @click="sel=c">
      <div class="cr"><div class="ca" :style="{background:c.cl}"></div>
        <div class="ci"><span class="cn">{{c.n}}</span><span class="ct" :class="c.t==='S'?'ts':'ta'">{{c.t}}</span><span class="cs">{{c.sy}}</span><span class="cd">{{c.d}}</span></div>
      </div>
    </div>
    <div class="overlay" v-if="sel" @click="sel=null"><div class="oc" @click.stop>
      <div class="cr"><div class="ca lg" :style="{background:sel.cl}"></div><div class="ci"><span class="cn">{{sel.n}}</span><span class="ct" :class="sel.t==='S'?'ts':'ta'">{{sel.t}}</span></div></div>
      <p class="odesc">{{sel.full}}</p><button class="btn" @click="sel=null">关闭</button>
    </div></div>
  </div>

  <!-- ===== GAME ===== -->
  <div v-else-if="pg==='game'" class="pg">
    <div class="tb"><span class="t1">实时对局分析</span></div>
    <div class="card"><div class="sec">当前排名</div><span class="bv">2 / 8</span></div>
    <div class="card ail"><div class="sec">AI建议</div><span class="bd">优先转型T0阵容 · 稳血上8人口</span></div>
    <div class="card"><div class="sec">风险等级</div><span class="bv yl">中等</span></div>
    <div class="card"><div class="sec">棋盘预览</div><div class="board"><div class="cell" v-for="i in 28" :key="i" :class="{ac:cs.includes(i)}" @click="tog(i)">{{i}}</div></div></div>
    <div class="card"><div class="sec">对手侦察</div><span class="bd">P3走8法师 · P5走海魔 · P7暗星</span></div>
    <button class="btn" @click="gs=!gs">{{gs?'更新数据':'开始新对局'}}</button>
    <div v-if="gs" class="live-dot">AI引擎运行中...</div>
  </div>

  <!-- ===== GUIDE ===== -->
  <div v-else-if="pg==='guide'" class="pg">
    <div class="tb"><span class="t1">版本攻略</span></div>
    <div class="card"><div class="sec">当前版本</div><span class="bv gl">S17.5 赛博城市</span></div>
    <div class="card" v-for="g in guides" :key="g.id"><span class="gn">{{g.t}}</span><span class="gd">{{g.d}}</span><span class="gtag">{{g.tag}}</span></div>
  </div>

  <!-- ===== MINE ===== -->
  <div v-else-if="pg==='mine'" class="pg">
    <div class="tb"><span class="t1">我的</span></div>
    <div class="card"><div class="sec">段位</div><span class="bv gl">钻石 II</span></div>
    <div class="card"><div class="sec">胜率</div><span class="bv gr">57%</span></div>
    <div class="card"><div class="sec">常用阵容</div><span class="bd">运营小法 · 幻灵皎月</span></div>
    <div class="card"><div class="sec">对局历史</div><span class="bd">最近12局 · 场均排名 3.4</span></div>
  </div>

  <!-- ===== TABBAR ===== -->
  <nav class="nav" v-if="pg!=='login'&&pg!=='match'">
    <button v-for="t in tabs" :key="t.id" :class="{a:pg===t.id}" @click="pg=t.id">{{t.n}}</button>
  </nav>
</div>
</template>

<script setup>
import {ref} from "vue";
const pg=ref("login"),ph=ref(""),pw=ref(""),err=ref(false),sel=ref(null),cs=ref([]),gs=ref(false);
const feats=[{t:"实时胜率预测",d:"每回合更新"},{t:"对手阵容侦察",d:"7家动态"},{t:"卡池余量追踪",d:"核心牌监控"},{t:"AI智能转阵",d:"最优路径"}];
const heroes=["Aatrox","Jinx","Yasuo","Akali"];
const tabs=[{id:"home",n:"首页"},{id:"comps",n:"阵容"},{id:"game",n:"对局"},{id:"guide",n:"攻略"},{id:"mine",n:"我的"}];
const comps=[{n:"运营小法",t:"S",sy:"7木灵·2法师",d:"34%吃鸡 80%前四",cl:"#7EC8E3",full:"2星小法开局=稳定吃分。3-1 D干找小法三星，升7凑7木灵成型。"},{n:"重装龙王",t:"S",sy:"4重装·3牧羊人·2神谕",d:"32%吃鸡 78%前四",cl:"#ED8936",full:"过渡平滑，5-1上9找薇古丝+巴德大成，当前版本T0真神。"},{n:"幻灵皎月",t:"S",sy:"5幻灵·3挑战者",d:"30%吃鸡 76%前四",cl:"#B8A9D4",full:"400层重铸器成型，皎月三件套稳前四。"},{n:"海魔金克丝",t:"A",sy:"3海魔·3挑战者",d:"25%吃鸡 72%前四",cl:"#F9E79F",full:"2费金克丝主C，海魔经济加速追三，版本吃分利器。"}];
const guides=[{id:1,t:"运营小法：T0木灵画全攻略",d:"2星小法开局=稳定吃分",tag:"阵容"},{id:2,t:"幻灵皎月：400层机制详解",d:"5幻灵新贵，皎月三件套",tag:"赌狗"},{id:3,t:"经济海克斯削后怎么玩？",d:"AI教你最优节奏",tag:"运营"},{id:4,t:"宝宝学院回归：赌狗崛起",d:"1%→8%史诗加强",tag:"版本"}];
const login=()=>ph.value?pg.value="home":err.value=true;
const tog=(i)=>{const x=cs.value.indexOf(i);x>=0?cs.value.splice(x,1):cs.value.push(i)};
</script>
