<!DOCTYPE html>
<html lang="tr"><head><meta charset="utf-8">
<meta name="viewport" content="width=device-width,initial-scale=1,maximum-scale=1">
<meta name="theme-color" content="#111111">
<title>Assembly takip</title>
<style>
*{box-sizing:border-box;-webkit-tap-highlight-color:transparent}
html,body{margin:0;height:100%;background:#111;color:#eee;font-family:system-ui,sans-serif}
body{display:flex;flex-direction:column;height:100dvh}
button,.btn,input,select{font:inherit;font-size:15px;color:#eee;background:#2a2a2a;border:1px solid #444;border-radius:8px;padding:6px 10px}
button:active,.btn:active{background:#444}
input[type=number]{-moz-appearance:textfield}
input[type=number]::-webkit-inner-spin-button{-webkit-appearance:none}
#top{padding:8px;display:flex;flex-direction:column;gap:8px;border-bottom:1px solid #333}
#tot{display:flex;justify-content:space-between;gap:8px;flex-wrap:wrap;font-size:14px;color:#aaa}
#tot b{color:#fff;font-weight:500}
.bars{display:grid;grid-template-columns:repeat(3,minmax(0,1fr));gap:8px}
.bar{background:#1b1b1b;border:1px solid #333;border-radius:10px;padding:8px}
.bar .n{font-size:12px;color:#aaa}
.bar .p{font-size:20px;font-weight:500}
.bar .k{font-size:11px;color:#888}
.track{height:6px;background:#333;border-radius:3px;margin:4px 0;overflow:hidden}
.fill{height:100%;width:0;border-radius:3px;transition:width .2s}
.tools{display:flex;gap:8px;flex-wrap:wrap}
.tools input[type=search]{flex:1;min-width:0}
.tools button,.tools .btn{padding:6px 12px;font-size:14px}
#wrap{flex:1;overflow:auto;min-height:0}
table{border-collapse:separate;border-spacing:0;width:100%;min-width:760px}
th{position:sticky;top:0;z-index:3;background:#1b1b1b;font-weight:500;font-size:12px;color:#aaa;padding:8px 6px;text-align:left;border-bottom:1px solid #333}
td{padding:5px 6px;border-bottom:1px solid #262626;vertical-align:middle}
.pos{position:sticky;left:0;z-index:2;background:#151515;min-width:130px}
th.pos{z-index:4;background:#1b1b1b}
.ed{width:100%;background:transparent;border:1px solid transparent;padding:4px 6px}
.ed:focus{border-color:#378ADD;background:#222;outline:none}
.sub{font-size:12px;color:#999;padding-top:0}
.num{width:72px;text-align:right}
.tw{font-weight:500;white-space:nowrap;text-align:right;padding-right:12px}
.sc{display:flex;align-items:center;gap:4px;padding:2px 4px;border-radius:8px;background:#1b1b1b;border:1px solid #333;width:max-content}
.sc.full{background:#0F3D2E;border-color:#1D9E75}
.sc.part{background:#3a2a0c;border-color:#BA7517}
.sin{width:48px;padding:4px;text-align:center;background:transparent;border:none}
.sin:focus{outline:1px solid #378ADD}
.of{font-size:12px;color:#aaa;min-width:26px}
.sc button{padding:3px 9px}
.x{padding:3px 10px;color:#f09595}
.empty{text-align:center;color:#888;padding:24px}
</style></head><body>
<div id="top">
<div id="tot"><span>Toplam ağırlık <b id="tw"></b></span><span><b id="tq"></b> adet · <b id="tn"></b> kalem</span></div>
<div class="bars" id="bars"></div>
<div class="tools">
<input id="q" type="search" placeholder="Assembly ara">
<select id="fl"><option value="all">Tümü</option><option value="0">İmalatı bitmeyen</option><option value="1">Boyası bitmeyen</option><option value="2">Sevkiyatı bitmeyen</option><option value="sent">Sevk edilenler</option></select>
</div>
<div class="tools">
<button id="add">+ Satır</button><button id="bk">Yedekle</button>
<label class="btn">Yükle<input type="file" id="imp" accept=".json,application/json" hidden></label>
<button id="csv">Excel</button>
</div>
</div>
<div id="wrap"><table>
<thead><tr><th class="pos">Assembly</th><th>Adet</th><th>Birim kg</th><th>Toplam kg</th><th>İmalat</th><th>Boya</th><th>Sevkiyat</th><th></th></tr></thead>
<tbody id="tb"></tbody></table></div>
<script>
const $=id=>document.getElementById(id);
const KEY='assembly_takip_v1';
const ST=['İmalat','Boya','Sevkiyat'],COL=['#378ADD','#BA7517','#1D9E75'];
const RAW=`MBS/1|BAKLA PL4*262|1|13.4
MBS/2|BAKLA PL4*1200|61|16.3
MBS/3|BAKLA PL4*263|1|13.4
MBS/4|BAKLA PL4*1200|8|15.6
MBS/5|BAKLA PL4*142|1|7.5
MBS/6|BAKLA PL4*314|1|17.3
MBS/7|BAKLA PL4*576|1|28.0
MBS/8|BAKLA PL4*1200|1|31.6
MBS/9|BAKLA PL4*1200|1|37.7
MBS/10|BAKLA PL4*545|4|21.7
MBS/11|BAKLA PL4*540|6|21.5
MBS/12|BAKLA PL4*142|1|7.5
MBS/13|BAKLA PL4*262|1|12.3
MBS/14|BAKLA PL4*465|3|8.0
MBS/15|BAKLA PL4*465|1|9.7
MBS/18|BAKLA PL4*598|1|27.8
MBS/19|BAKLA PL4*262|1|12.4
MBS/20|BAKLA PL4*311|1|14.3
MBS/21|BAKLA PL4*291|1|13.5
MBS/22|BAKLA PL4*545|1|21.1
MBS/23|BAKLA PL4*545|1|21.3
MBS/24|BAKLA PL4*465|1|11.8
MBS/25|BAKLA PL4*465|1|11.8
MBS/26|BAKLA PL4*415|4|16.1
MBS/27|BAKLA PL4*510|8|20.3
MBS/28|BAKLA PL4*265|3|3.5
MBS/29|BAKLA PL4*265|1|6.8
MBS/30|BAKLA PL4*265|2|6.8
MBS/31|BAKLA PL4*414|2|16.5
MBS/32|BAKLA PL4*292|1|13.6
MBS/33|BAKLA PL4*585|1|25.5
MBS/34|BAKLA PL4*585|1|28.4
MBS/35|BAKLA PL4*265|1|3.4
MBS/36|BAKLA PL4*305|1|14.1
MBS/37|BAKLA PL4*259|1|12.7
MBS/38|BAKLA PL4*415|2|16.6
MBS/39|BAKLA PL4*265|1|6.8
MBS/40|BAKLA PL4*698|1|20.7
MBS/41|BAKLA PL4*630|1|18.7
MKL/1|KOLON HEA200|1|88.3
MKL/2|KOLON HEA200|1|88.3
MKL/3|KOLON HEA200|1|262.1
MKL/4|KOLON HEA200|1|262.1
MKL/5|KOLON HEA200|1|204.8
MKL/6|KOLON HEA200|1|204.8`;

const uid=()=>Date.now().toString(36)+Math.random().toString(36).slice(2,6);
const num=v=>parseFloat(String(v).replace(',','.'))||0;
const f1=n=>n.toLocaleString('tr-TR',{minimumFractionDigits:1,maximumFractionDigits:1});
const esc=s=>String(s).replace(/&/g,'&amp;').replace(/"/g,'&quot;').replace(/</g,'&lt;');
function norm(x){
  const qty=Math.max(1,Math.round(+x.qty||1));
  const d=[0,1,2].map(i=>Math.min(qty,Math.max(0,Math.round(+(x.d&&x.d[i])||0))));
  return{id:String(x.id||uid()),pos:String(x.pos||''),info:String(x.info||''),qty,w:Math.max(0,+x.w||0),d};
}
function defaults(){
  return RAW.split('\n').map(l=>{const a=l.split('|');return norm({pos:a[0],info:a[1],qty:a[2],w:a[3]})});
}
let rows=null;
try{rows=JSON.parse(localStorage.getItem(KEY))}catch(e){}
rows=Array.isArray(rows)?rows.map(norm):defaults();
const save=()=>{try{localStorage.setItem(KEY,JSON.stringify(rows))}catch(e){}};

$('bars').innerHTML=ST.map((n,s)=>'<div class="bar"><div class="n">'+n+'</div><div class="p" id="p'+s+'"></div><div class="track"><div class="fill" id="f'+s+'" style="background:'+COL[s]+'"></div></div><div class="k" id="k'+s+'"></div></div>').join('');

function summary(){
  let tw=0,tq=0;const dn=[0,0,0];
  rows.forEach(r=>{tw+=r.qty*r.w;tq+=r.qty;for(let s=0;s<3;s++)dn[s]+=r.d[s]*r.w});
  $('tw').textContent=f1(tw)+' kg ('+(tw/1000).toLocaleString('tr-TR',{maximumFractionDigits:2})+' t)';
  $('tq').textContent=tq;$('tn').textContent=rows.length;
  for(let s=0;s<3;s++){
    const p=tw?dn[s]/tw*100:0;
    $('p'+s).textContent='%'+f1(p);
    $('f'+s).style.width=Math.min(100,p)+'%';
    $('k'+s).textContent=f1(dn[s])+' kg';
  }
}
function paint(tr,r,force){
  tr.querySelector('.tw').textContent=f1(r.qty*r.w);
  tr.querySelectorAll('.sc').forEach(c=>{
    const s=+c.dataset.s,v=r.d[s],i=c.querySelector('.sin');
    if(force||document.activeElement!==i)i.value=v;
    i.max=r.qty;
    c.querySelector('.of').textContent='/'+r.qty;
    c.className='sc'+(v>=r.qty?' full':v>0?' part':'');
  });
}
function rowEl(r){
  const tr=document.createElement('tr');tr.dataset.id=r.id;
  tr.innerHTML='<td class="pos"><input class="ed" data-f="pos" value="'+esc(r.pos)+'"><input class="ed sub" data-f="info" value="'+esc(r.info)+'"></td>'
  +'<td><input class="ed num" data-f="qty" type="number" inputmode="numeric" min="1" value="'+r.qty+'"></td>'
  +'<td><input class="ed num" data-f="w" type="number" inputmode="decimal" step="0.1" min="0" value="'+r.w+'"></td>'
  +'<td class="tw"></td>'
  +[0,1,2].map(s=>'<td><div class="sc" data-s="'+s+'"><input class="sin" type="number" inputmode="numeric" min="0"><span class="of"></span><button data-ok="'+s+'" aria-label="'+ST[s]+' tamam">✓</button></div></td>').join('')
  +'<td><button class="x" data-del="1" aria-label="Sil">×</button></td>';
  paint(tr,r,true);return tr;
}
function visible(r){
  const q=$('q').value.trim().toLocaleLowerCase('tr'),fl=$('fl').value;
  if(q&&!(r.pos+' '+r.info).toLocaleLowerCase('tr').includes(q))return false;
  if(fl==='0'||fl==='1'||fl==='2')return r.d[+fl]<r.qty;
  if(fl==='sent')return r.d[2]>=r.qty;
  return true;
}
function render(){
  const tb=$('tb');tb.innerHTML='';
  const list=rows.filter(visible);
  list.forEach(r=>tb.append(rowEl(r)));
  if(!list.length)tb.innerHTML='<tr><td colspan="8" class="empty">Kayıt yok</td></tr>';
}
const getRow=el=>{const tr=el.closest('tr');return tr&&tr.dataset.id?[tr,rows.find(x=>x.id===tr.dataset.id)]:[null,null]};

$('tb').addEventListener('input',e=>{
  const t=e.target,[tr,r]=getRow(t);if(!r)return;
  const f=t.dataset.f;
  if(f==='pos'||f==='info')r[f]=t.value;
  else if(f==='qty'){r.qty=Math.max(1,Math.round(num(t.value))||1);r.d=r.d.map(x=>Math.min(x,r.qty))}
  else if(f==='w')r.w=Math.max(0,num(t.value));
  else if(t.classList.contains('sin')){const s=+t.closest('.sc').dataset.s;r.d[s]=Math.min(r.qty,Math.max(0,Math.round(num(t.value))))}
  else return;
  paint(tr,r);summary();save();
});
$('tb').addEventListener('change',e=>{
  const t=e.target,[tr,r]=getRow(t);if(!r)return;
  if(t.dataset.f==='qty')t.value=r.qty;
  if(t.dataset.f==='w')t.value=r.w;
  paint(tr,r,true);
});
$('tb').addEventListener('click',e=>{
  const b=e.target.closest('button');if(!b)return;
  const [tr,r]=getRow(b);if(!r)return;
  if(b.dataset.ok!==undefined){
    const s=+b.dataset.ok;r.d[s]=r.d[s]>=r.qty?0:r.qty;paint(tr,r,true);
  }else if(b.dataset.del){
    if(!confirm('"'+r.pos+'" silinsin mi?'))return;
    rows=rows.filter(x=>x!==r);tr.remove();
  }else return;
  summary();save();
});

$('q').oninput=render;
$('fl').onchange=render;
$('add').onclick=()=>{
  rows.push(norm({pos:'YENİ',info:'',qty:1,w:0}));
  $('q').value='';$('fl').value='all';render();summary();save();
  const tr=$('tb').lastElementChild;tr.scrollIntoView({block:'center'});
  const i=tr.querySelector('[data-f=pos]');i.focus();i.select();
};
function dl(name,text,type){
  const a=document.createElement('a');a.href=URL.createObjectURL(new Blob([text],{type}));
  a.download=name;a.click();setTimeout(()=>URL.revokeObjectURL(a.href),1000);
}
$('bk').onclick=()=>dl('assembly_takip.json',JSON.stringify(rows),'application/json');
$('csv').onclick=()=>{
  const n=x=>String(x).replace('.',','),q=v=>'"'+String(v).replace(/"/g,'""')+'"';
  const L=['Assembly;Bilgi;Adet;Birim kg;Toplam kg;İmalat;Boya;Sevkiyat'];
  rows.forEach(r=>L.push([q(r.pos),q(r.info),r.qty,n(r.w),n(Math.round(r.qty*r.w*10)/10),r.d[0],r.d[1],r.d[2]].join(';')));
  dl('assembly_takip.csv','\ufeff'+L.join('\r\n'),'text/csv');
};
$('imp').onchange=e=>{
  const f=e.target.files[0];if(!f)return;
  const rd=new FileReader();
  rd.onload=()=>{
    try{
      const a=JSON.parse(rd.result);if(!Array.isArray(a))throw 0;
      if(!confirm(a.length+' kayıt yüklenecek, mevcut liste silinecek. Devam edilsin mi?'))return;
      rows=a.map(norm);save();render();summary();
    }catch(_){alert('Dosya okunamadı')}
  };
  rd.readAsText(f);e.target.value='';
};
summary();render();
</script></body></html>
