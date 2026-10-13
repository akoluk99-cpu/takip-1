<!DOCTYPE html>
<html lang="tr"><head><meta charset="utf-8">
<meta name="viewport" content="width=device-width,initial-scale=1,maximum-scale=1">
<meta name="theme-color" content="#111111">
<title>Assembly takip</title>
<style>
*{box-sizing:border-box;-webkit-tap-highlight-color:transparent}
html,body{margin:0;height:100%;background:#111;color:#eee;font-family:system-ui,sans-serif}
body{display:flex;flex-direction:column;height:100dvh}
button,.btn,input,select,textarea{font:inherit;font-size:15px;color:#eee;background:#2a2a2a;border:1px solid #444;border-radius:8px;padding:6px 10px}
button:active,.btn:active{background:#444}
input[type=number]{-moz-appearance:textfield}
input[type=number]::-webkit-inner-spin-button{-webkit-appearance:none}
#top{padding:8px;display:flex;flex-direction:column;gap:8px;border-bottom:1px solid #333}
#tot{display:flex;justify-content:space-between;gap:8px;flex-wrap:wrap;font-size:14px;color:#aaa}
#tot b{color:#fff;font-weight:500}
#st{font-size:12px}
#st.on{color:#5DCAA5}#st.off{color:#F09595}
#warn{display:none;background:#3a2a0c;border:1px solid #BA7517;border-radius:8px;padding:8px;font-size:14px;color:#EF9F27}
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
#ls{flex:1;min-width:0;font-weight:500}
#wrap{flex:1;overflow:auto;min-height:0}
table{border-collapse:separate;border-spacing:0;width:100%;min-width:760px}
th{position:sticky;top:0;z-index:3;background:#1b1b1b;font-weight:500;font-size:12px;color:#aaa;padding:8px 6px;text-align:left;border-bottom:1px solid #333}
td{padding:5px 6px;border-bottom:1px solid #262626;vertical-align:middle}
.pos{position:sticky;left:0;z-index:2;background:#151515;min-width:130px}
th.pos{z-index:4;background:#1b1b1b}
.ed{width:100%;background:transparent;border:1px solid transparent;padding:4px 6px}
.ed:focus{border-color:#378ADD;background:#222;outline:none}
.sub{font-size:12px;color:#999}
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
#imd{display:none;position:fixed;inset:0;background:rgba(0,0,0,.6);z-index:20;align-items:flex-end}
#imd.on{display:flex}
#imc{width:100%;max-height:92dvh;overflow-y:auto;background:#222;border-top:1px solid #555;padding:12px;display:flex;flex-direction:column;gap:8px}
#imc input,#imc select,#imc textarea{width:100%}
#imc label.l{font-size:13px;color:#aaa}
#ist{font-size:13px;color:#aaa}
#prev{white-space:pre-line;font-size:13px;color:#ccc}
.row{display:flex;gap:8px}
.row button{flex:1}
</style></head><body>
<div id="top">
<div id="warn">Veritabanı adresi girilmemiş. Kodda BURAYA_URL_YAPISTIR yazan yere Firebase adresini yapıştır.</div>
<div id="tot"><span>Toplam ağırlık <b id="tw"></b></span><span><b id="tq"></b> adet · <b id="tn"></b> kalem</span><span id="st" class="off">○ Bağlanıyor…</span></div>
<div class="bars" id="bars"></div>
<div class="tools">
<select id="ls"></select>
<button id="nl">+ Liste</button><button id="mo" aria-label="Diğer işlemler">⋯</button>
</div>
<div class="tools" id="more" style="display:none">
<button id="rn">Adlandır</button><button id="dl">Listeyi sil</button><button id="bk">Yedekle</button><button id="csv">Excel indir</button>
</div>
<div class="tools">
<input id="q" type="search" placeholder="Assembly ara">
<select id="fl"><option value="all">Tümü</option><option value="0">İmalatı bitmeyen</option><option value="1">Boyası bitmeyen</option><option value="2">Sevkiyatı bitmeyen</option><option value="sent">Sevk edilenler</option></select>
</div>
<div class="tools"><button id="add">+ Satır</button><button id="imp">İçe aktar (Word / Excel)</button></div>
</div>
<div id="wrap"><table>
<thead><tr><th class="pos">Assembly</th><th>Adet</th><th>Birim kg</th><th>Toplam kg</th><th>İmalat</th><th>Boya</th><th>Sevkiyat</th><th></th></tr></thead>
<tbody id="tb"><tr><td colspan="8" class="empty">Yükleniyor…</td></tr></tbody></table></div>

<div id="imd"><div id="imc">
<b>Listeye içe aktar</b>
<label class="btn" style="display:block;text-align:center">Dosya seç (Excel, Word, CSV)<input type="file" id="ifile" accept=".xlsx,.xls,.csv,.docx,.doc,.txt,.json" hidden></label>
<div id="ist"></div>
<textarea id="ipaste" rows="4" placeholder="…ya da Word veya Excel'den kopyaladığın tabloyu buraya yapıştır"></textarea>
<label class="l">Ağırlık sütunu neyi gösteriyor?</label>
<select id="imode"><option value="unit">Birim (tek parça) ağırlığı</option><option value="total">Toplam ağırlık</option></select>
<label class="l">Hedef</label>
<select id="itgt"><option value="new">Yeni liste oluştur</option><option value="cur">Seçili listeye ekle</option></select>
<input id="iname" placeholder="Yeni liste adı">
<div id="prev"></div>
<div class="row"><button id="idoit" disabled>İçe aktar</button><button id="ix">İptal</button></div>
</div></div>

<script src="https://www.gstatic.com/firebasejs/10.12.2/firebase-app-compat.js"></script>
<script src="https://www.gstatic.com/firebasejs/10.12.2/firebase-database-compat.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/xlsx/0.18.5/xlsx.full.min.js"></script>
<script src="https://unpkg.com/mammoth@1.6.0/mammoth.browser.min.js"></script>
<script>
const $=id=>document.getElementById(id);
const DB_URL='https://takip-85fe0-default-rtdb.europe-west1.firebasedatabase.app/';
const ST=['İmalat','Boya','Sevkiyat'],COL=['#378ADD','#BA7517','#1D9E75'];

function parseNum(v){
  let s=String(v==null?'':v).trim().replace(/\s/g,'');
  if(!s)return NaN;
  if(s.indexOf(',')>=0&&s.indexOf('.')>=0){
    if(s.lastIndexOf(',')>s.lastIndexOf('.'))s=s.replace(/\./g,'').replace(',','.');
    else s=s.replace(/,/g,'');
  }else s=s.replace(',','.');
  return /^-?(\d+\.?\d*|\.\d+)$/.test(s)?parseFloat(s):NaN;
}
const num=v=>{const n=parseNum(v);return isNaN(n)?0:n};
const f1=n=>n.toLocaleString('tr-TR',{minimumFractionDigits:1,maximumFractionDigits:1});
const esc=s=>String(s).replace(/&/g,'&amp;').replace(/"/g,'&quot;').replace(/</g,'&lt;');
function norm(id,x){
  const qty=Math.max(1,Math.round(+x.qty||1));
  const d=[0,1,2].map(i=>Math.min(qty,Math.max(0,Math.round(+x['d'+i]||0))));
  return{id:id,pos:String(x.pos||''),info:String(x.info||''),qty:qty,w:Math.max(0,+x.w||0),d:d,o:+x.o||0};
}
const toDb=r=>({pos:r.pos,info:r.info,qty:r.qty,w:r.w,d0:r.d[0],d1:r.d[1],d2:r.d[2],o:r.o});

let db=null,R=null,cur=null,want=null,rows=[],lists=[],lastKey=null;
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
  const setv=(i,v)=>{if(force||document.activeElement!==i)i.value=v};
  tr.querySelector('.tw').textContent=f1(r.qty*r.w);
  tr.querySelectorAll('[data-f]').forEach(i=>setv(i,r[i.dataset.f]));
  tr.querySelectorAll('.sc').forEach(c=>{
    const s=+c.dataset.s,v=r.d[s],i=c.querySelector('.sin');
    setv(i,v);i.max=r.qty;
    c.querySelector('.of').textContent='/'+r.qty;
    c.className='sc'+(v>=r.qty?' full':v>0?' part':'');
  });
}
function rowEl(r){
  const tr=document.createElement('tr');tr.dataset.id=r.id;
  tr.innerHTML='<td class="pos"><input class="ed" data-f="pos"><input class="ed sub" data-f="info"></td>'
  +'<td><input class="ed num" data-f="qty" type="number" inputmode="numeric" min="1"></td>'
  +'<td><input class="ed num" data-f="w" type="number" inputmode="decimal" step="0.1" min="0"></td>'
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
function sync(){
  summary();
  const key=rows.map(r=>r.id).join(',');
  if(key!==lastKey){lastKey=key;render();return}
  $('tb').querySelectorAll('tr[data-id]').forEach(tr=>{
    const r=rows.find(x=>x.id===tr.dataset.id);if(r)paint(tr,r,false);
  });
}

/* ---------- listeler ---------- */
function rowsH(snap){
  const v=snap.val()||{};
  rows=Object.keys(v).map(id=>norm(id,v[id])).sort((a,b)=>a.o-b.o);
  sync();
}
function selectList(id){
  if(R)R.off('value',rowsH);
  cur=id;R=null;rows=[];lastKey=null;summary();
  if(!id){
    $('tb').innerHTML='<tr><td colspan="8" class="empty">Liste yok. "+ Liste" ile yeni liste oluştur ya da içe aktar.</td></tr>';
    return;
  }
  try{localStorage.setItem('son_liste',id)}catch(e){}
  try{history.replaceState(null,'','#'+encodeURIComponent(id))}catch(e){}
  $('tb').innerHTML='<tr><td colspan="8" class="empty">Yükleniyor…</td></tr>';
  R=db.ref('listRows/'+id);
  R.on('value',rowsH,()=>{$('st').textContent='○ Erişim izni yok (Rules ayarını kontrol et)';$('st').className='off'});
}
function chooseCurrent(){
  let t=null;
  if(want&&lists.some(l=>l.id===want)){t=want;want=null}
  else if(cur&&lists.some(l=>l.id===cur))t=cur;
  else{
    let h='',s='';
    try{h=decodeURIComponent(location.hash.slice(1))}catch(e){}
    try{s=localStorage.getItem('son_liste')||''}catch(e){}
    t=[h,s].find(x=>x&&lists.some(l=>l.id===x))||(lists[0]&&lists[0].id)||null;
  }
  $('ls').innerHTML=lists.map(l=>'<option value="'+esc(l.id)+'">'+esc(l.name)+'</option>').join('');
  if(t)$('ls').value=t;
  if(t!==cur||t===null)selectList(t);
}
function pick(id){want=id;chooseCurrent()}
const curName=()=>{const l=lists.find(x=>x.id===cur);return l?l.name:'liste'};
const maxO=a=>a.reduce((m,x)=>Math.max(m,x.o||0),0);

$('ls').onchange=()=>pick($('ls').value);
$('nl').onclick=()=>{
  if(!db)return;
  const n=(prompt('Yeni liste adı')||'').trim();if(!n)return;
  const id='l'+Date.now().toString(36)+Math.random().toString(36).slice(2,5);
  db.ref('listMeta/'+id).set({name:n,o:maxO(lists)+1});pick(id);
};
$('mo').onclick=()=>{const m=$('more');m.style.display=m.style.display==='none'?'flex':'none'};
$('rn').onclick=()=>{
  if(!cur)return;
  const n=(prompt('Liste adı',curName())||'').trim();if(!n)return;
  db.ref('listMeta/'+cur+'/name').set(n);
};
$('dl').onclick=()=>{
  if(!cur)return;
  if(!confirm('"'+curName()+'" listesi ve içindeki '+rows.length+' satır herkesten silinsin mi? Bu işlem geri alınamaz.'))return;
  const id=cur;db.ref('listMeta/'+id).remove();db.ref('listRows/'+id).remove();
};

/* ---------- firebase ---------- */
if(DB_URL.indexOf('BURAYA')>=0){
  $('warn').style.display='block';
  $('tb').innerHTML='<tr><td colspan="8" class="empty">Veritabanı bağlı değil</td></tr>';
}else{
  firebase.initializeApp({databaseURL:DB_URL});
  db=firebase.database();
  db.ref('.info/connected').on('value',s=>{
    $('st').textContent=s.val()?'● Canlı':'○ Bağlantı yok';
    $('st').className=s.val()?'on':'off';
  });
  db.ref('meta/listsInit').once('value').then(s=>{
    if(s.val()===true)return;
    return db.ref('listMeta').once('value').then(m=>{
      if(m.exists())return;
      return db.ref('rows').once('value').then(old=>{
        const u={};
        if(old.exists())u['listRows/l_ilk']=old.val();
        u['listMeta/l_ilk']={name:'Ana liste',o:1};
        return db.ref().update(u);
      });
    }).then(()=>db.ref('meta/listsInit').set(true));
  }).catch(()=>{}).then(()=>{
    db.ref('listMeta').on('value',snap=>{
      const v=snap.val()||{};
      lists=Object.keys(v).map(id=>({id:id,name:String(v[id].name||'Liste'),o:+v[id].o||0})).sort((a,b)=>a.o-b.o);
      chooseCurrent();
    },()=>{$('st').textContent='○ Erişim izni yok (Rules ayarını kontrol et)';$('st').className='off'});
  });
}

/* ---------- satır düzenleme ---------- */
function getRow(el){
  const tr=el.closest('tr');
  if(!tr||!tr.dataset.id)return[null,null];
  return[tr,rows.find(x=>x.id===tr.dataset.id)];
}
$('tb').addEventListener('input',e=>{
  if(!R)return;
  const t=e.target,r=getRow(t)[1];if(!r)return;
  const f=t.dataset.f,ref=R.child(r.id);
  if(f==='pos'||f==='info'){const u={};u[f]=t.value;ref.update(u)}
  else if(f==='qty'){
    if(t.value==='')return;
    const q=Math.max(1,Math.round(num(t.value))||1);
    ref.update({qty:q,d0:Math.min(r.d[0],q),d1:Math.min(r.d[1],q),d2:Math.min(r.d[2],q)});
  }else if(f==='w'){
    if(t.value==='')return;
    ref.update({w:Math.max(0,num(t.value))});
  }else if(t.classList.contains('sin')){
    if(t.value==='')return;
    const s=+t.closest('.sc').dataset.s,u={};
    u['d'+s]=Math.min(r.qty,Math.max(0,Math.round(num(t.value))));
    ref.update(u);
  }
});
$('tb').addEventListener('change',e=>{
  const x=getRow(e.target);if(x[1])paint(x[0],x[1],true);
});
$('tb').addEventListener('click',e=>{
  if(!R)return;
  const b=e.target.closest('button');if(!b)return;
  const r=getRow(b)[1];if(!r)return;
  if(b.dataset.ok!==undefined){
    const s=+b.dataset.ok,u={};
    u['d'+s]=r.d[s]>=r.qty?0:r.qty;
    R.child(r.id).update(u);
  }else if(b.dataset.del){
    if(!confirm('"'+r.pos+'" silinsin mi? Bu herkesten silinir.'))return;
    R.child(r.id).remove();
  }
});
$('q').oninput=render;
$('fl').onchange=render;
$('add').onclick=()=>{
  if(!R){alert('Önce bir liste seç ya da oluştur');return}
  $('q').value='';$('fl').value='all';
  const ref=R.push(toDb({pos:'YENİ',info:'',qty:1,w:0,d:[0,0,0],o:maxO(rows)+1}));
  const id=ref.key;
  setTimeout(()=>{
    const tr=$('tb').querySelector('[data-id="'+id+'"]');
    if(tr){tr.scrollIntoView({block:'center'});const i=tr.querySelector('[data-f=pos]');i.focus();i.select()}
  },150);
};

/* ---------- dışa aktarma ---------- */
function dl(name,text,type){
  const a=document.createElement('a');a.href=URL.createObjectURL(new Blob([text],{type:type}));
  a.download=name;a.click();setTimeout(()=>URL.revokeObjectURL(a.href),1000);
}
const safeName=()=>curName().replace(/[^\wÇçĞğİıÖöŞşÜü -]/g,'_');
$('bk').onclick=()=>dl(safeName()+'.json',JSON.stringify(rows.map(r=>({pos:r.pos,info:r.info,qty:r.qty,w:r.w,d:r.d}))),'application/json');
$('csv').onclick=()=>{
  const n=x=>String(x).replace('.',','),q=v=>'"'+String(v).replace(/"/g,'""')+'"';
  const L=['Assembly;Bilgi;Adet;Birim kg;Toplam kg;İmalat;Boya;Sevkiyat'];
  rows.forEach(r=>L.push([q(r.pos),q(r.info),r.qty,n(r.w),n(Math.round(r.qty*r.w*10)/10),r.d[0],r.d[1],r.d[2]].join(';')));
  dl(safeName()+'.csv','\ufeff'+L.join('\r\n'),'text/csv');
};

/* ---------- içe aktarma ---------- */
function role(h){
  h=String(h==null?'':h).toLocaleLowerCase('tr').replace(/[.\s_]+/g,' ').trim();
  if(!h)return'';
  if(/toplam|total/.test(h)&&/ağ|ag|kg|weight|gewicht/.test(h))return'tw';
  if(/ağırlık|agirlik|weight|gewicht|^kg/.test(h)||/\(kg\)/.test(h))return'w';
  if(/^(adet|qty|quantity|miktar|no|sayı|sayi)$/.test(h)||/^(adet|qty|miktar)/.test(h))return'qty';
  if(/area|alan|m2|m²|m ²/.test(h))return'skip';
  if(/asmbly|assembly|^pos|poz|parça|parca|^kod|mark/.test(h))return'pos';
  if(/name|profile|profil|açıklama|aciklama|tanım|tanim|isim|ürün|urun|^ad$/.test(h))return'info';
  return'';
}
function mk(pos,info,qty,w,mode){
  if(mode==='total'&&qty)w=w/qty;
  return{pos:pos,info:info,qty:qty,w:Math.round(w*100)/100};
}
function heur(t,mode){
  t=t.map(x=>String(x==null?'':x).trim()).filter(x=>x!=='');
  if(t.length<3||!/^\d+$/.test(t[1]))return null;
  const w=parseNum(t[t.length-1]);if(isNaN(w))return null;
  const qty=parseInt(t[1],10);if(qty<1)return null;
  const mid=t.slice(2,-1);
  while(mid.length&&!isNaN(parseNum(mid[mid.length-1])))mid.pop();
  return mk(t[0],mid.join(' '),qty,w,mode);
}
function parseGrid(grid,noHeader,mode){
  const out=[];let hi=-1,map=null;
  if(!noHeader){
    for(let i=0;i<Math.min(grid.length,20)&&hi<0;i++){
      const m={},seen={};let n=0;
      (grid[i]||[]).forEach((c,j)=>{
        const r=role(c);if(!r||r==='skip')return;
        if(r==='info'){(m.info=m.info||[]).push(j)}else if(m[r]==null)m[r]=j;
        if(!seen[r]){seen[r]=1;n++}
      });
      if(n>=2){hi=i;map=m}
    }
  }
  for(let i=hi+1;i<grid.length;i++){
    const row=grid[i]||[];let r=null;
    if(map){
      const cell=j=>{const v=j==null?'':row[j];return String(v==null?'':v).trim()};
      const pos=cell(map.pos!=null?map.pos:0);
      if(!pos||/^(toplam|total|genel)/i.test(pos))continue;
      let qty=1;
      if(map.qty!=null){const q=parseNum(cell(map.qty));if(isNaN(q)||q<1)continue;qty=Math.round(q)}
      let wv=NaN,m2=mode;
      if(map.w!=null)wv=parseNum(cell(map.w));
      else if(map.tw!=null){wv=parseNum(cell(map.tw));m2='total'}
      if(isNaN(wv))wv=0;
      r=mk(pos,(map.info||[]).map(cell).filter(Boolean).join(' '),qty,wv,m2);
    }else r=heur(row,mode);
    if(r)out.push(r);
  }
  return out;
}
function textGrid(text){
  const lines=text.split(/\r?\n/).filter(l=>l.trim()!=='');
  const tabbed=lines.filter(l=>l.indexOf('\t')>=0).length>lines.length/2;
  return{grid:lines.map(l=>tabbed?l.split('\t'):l.trim().split(/\s+/)),noHeader:!tabbed};
}
function readBook(wb){
  return wb.SheetNames.map(n=>({name:n,grid:XLSX.utils.sheet_to_json(wb.Sheets[n],{header:1,defval:''}),noHeader:false}));
}
async function readDocx(buf){
  const h=await mammoth.convertToHtml({arrayBuffer:buf});
  const doc=new DOMParser().parseFromString(h.value,'text/html');
  const tabs=[].slice.call(doc.querySelectorAll('table'));
  const gs=tabs.map((t,i)=>({
    name:tabs.length>1?'Tablo '+(i+1):'',
    grid:[].slice.call(t.querySelectorAll('tr')).map(tr=>[].slice.call(tr.children).map(c=>c.textContent.trim())),
    noHeader:false
  }));
  const raw=await mammoth.extractRawText({arrayBuffer:buf});
  const tg=textGrid(raw.value);
  if(!gs.length)gs.push({name:'',grid:tg.grid,noHeader:tg.noHeader});
  else gs[0].alt={grid:tg.grid,noHeader:tg.noHeader};
  return gs;
}

let src=[],groups=[];
function reparse(){
  const mode=$('imode').value;
  groups=src.map(g=>{
    let r=g.rows;
    if(!r){r=parseGrid(g.grid,g.noHeader,mode);if(!r.length&&g.alt)r=parseGrid(g.alt.grid,g.alt.noHeader,mode)}
    return{name:g.name,rows:r};
  }).filter(g=>g.rows.length);
  const p=$('prev'),b=$('idoit');
  if(!src.length){p.textContent='';b.disabled=true;return}
  if(!groups.length){
    p.textContent='Satır bulunamadı. Sütunlar şu sırada olmalı: assembly adı, adet, ağırlık. Başlık satırı olabilir.';
    b.disabled=true;return;
  }
  let n=0,w=0;groups.forEach(g=>g.rows.forEach(r=>{n++;w+=r.qty*r.w}));
  const s=groups[0].rows.slice(0,3).map(r=>r.pos+' · '+r.qty+' adet · '+r.w+' kg').join('\n');
  p.textContent=n+' satır'+(groups.length>1?' ('+groups.length+' sayfa/tablo, her biri ayrı liste olur)':'')+', toplam '+f1(w)+' kg\n'+s;
  b.disabled=false;
}
function tgtUI(){$('iname').style.display=$('itgt').value==='new'?'block':'none'}
function openImport(){
  src=[];groups=[];
  $('ipaste').value='';$('ist').textContent='';$('iname').value='';$('prev').textContent='';$('idoit').disabled=true;
  $('itgt').querySelector('[value=cur]').disabled=!cur;
  $('itgt').value='new';tgtUI();
  $('imd').classList.add('on');
}
$('imp').onclick=()=>{if(!db){alert('Veritabanı bağlı değil');return}openImport()};
$('ix').onclick=()=>$('imd').classList.remove('on');
$('itgt').onchange=tgtUI;
$('imode').onchange=reparse;
let pt;
$('ipaste').oninput=()=>{
  clearTimeout(pt);
  pt=setTimeout(()=>{
    const v=$('ipaste').value;
    if(v.trim()){const g=textGrid(v);src=[{name:'',grid:g.grid,noHeader:g.noHeader}];$('ist').textContent=''}
    else src=[];
    reparse();
  },300);
};
$('ifile').onchange=async e=>{
  const f=e.target.files[0];e.target.value='';if(!f)return;
  const ext=(f.name.split('.').pop()||'').toLowerCase();
  $('ist').textContent='Okunuyor…';$('ipaste').value='';
  try{
    let gs;
    if(ext==='json'){
      const a=JSON.parse(await f.text());if(!Array.isArray(a))throw new Error('Geçersiz yedek dosyası');
      gs=[{name:'',rows:a.map(x=>({pos:String(x.pos||''),info:String(x.info||''),qty:Math.max(1,Math.round(+x.qty||1)),w:Math.max(0,+x.w||0),d:x.d}))}];
    }else if(ext==='txt'){
      const g=textGrid(await f.text());gs=[{name:'',grid:g.grid,noHeader:g.noHeader}];
    }else if(ext==='csv'||ext==='xlsx'||ext==='xls'){
      if(typeof XLSX==='undefined')throw new Error('Excel kütüphanesi yüklenemedi, internet bağlantını kontrol et');
      const wb=ext==='csv'?XLSX.read(await f.text(),{type:'string'}):XLSX.read(await f.arrayBuffer(),{type:'array'});
      gs=readBook(wb);
    }else if(ext==='docx'){
      if(typeof mammoth==='undefined')throw new Error('Word kütüphanesi yüklenemedi, internet bağlantını kontrol et');
      gs=await readDocx(await f.arrayBuffer());
    }else if(ext==='doc'){
      throw new Error('Eski .doc biçimi desteklenmiyor. Word\'de "Farklı kaydet" ile .docx yapıp yükle.');
    }else throw new Error('Desteklenmeyen dosya türü');
    src=gs;
    $('ist').textContent=f.name;
    if(!$('iname').value)$('iname').value=f.name.replace(/\.[^.]+$/,'');
    reparse();
  }catch(err){
    src=[];groups=[];reparse();
    $('ist').textContent='Hata: '+(err&&err.message?err.message:'dosya okunamadı');
  }
};
function rowsObj(list,start,pre){
  const o={};
  list.forEach((r,i)=>{
    const d=(r.d||[0,0,0]).map(x=>Math.min(r.qty,Math.max(0,Math.round(+x||0))));
    o[pre+String(i).padStart(4,'0')]=toDb({pos:r.pos,info:r.info,qty:r.qty,w:r.w,d:d,o:start+i});
  });
  return o;
}
$('idoit').onclick=()=>{
  if(!groups.length||!db)return;
  const stamp=Date.now().toString(36);
  if($('itgt').value==='new'){
    const base=$('iname').value.trim()||'Yeni liste';
    let o=maxO(lists),first=null;
    groups.forEach((g,gi)=>{
      const id='l'+stamp+gi;o++;
      const nm=groups.length>1?base+' - '+(g.name||('Liste '+(gi+1))):base;
      db.ref('listMeta/'+id).set({name:nm,o:o});
      db.ref('listRows/'+id).update(rowsObj(g.rows,0,'r'+stamp+'_'));
      if(!first)first=id;
    });
    pick(first);
  }else if(cur){
    let start=maxO(rows)+1;
    groups.forEach((g,gi)=>{
      R.update(rowsObj(g.rows,start,'r'+stamp+gi+'_'));
      start+=g.rows.length;
    });
  }
  $('imd').classList.remove('on');
};
</script></body></html>
