<script setup>
import { onMounted , reactive} from "vue";
import liff from "@line/liff";

const sysdata = reactive({
  language : "",
  version : "",
  isInClient : "",
  isLoggedIn : "",
  os : "",
  lineVersion : "",
}); 

const userdata = reactive({
  context : "",
  profile : "",
  user : "",
}); 


onMounted(async () => {
  liff
  .init({
    liffId: "1657732539-zn43wJXG", // Use own liffId
  })
  .then(() => {
    sysdata.language = liff.getLanguage(); // String。引用 LIFF SDK 的頁面，頁面中的 lang 值
    sysdata.version = liff.getVersion(); // String。LIFF SDK 的版本
    sysdata.isInClient = liff.isInClient(); // Boolean。回傳是否由 LINE App 存取
    sysdata.isLoggedIn = liff.isLoggedIn(); // Boolean。使用者是否登入 LINE 帳號。true 時，可呼叫需要 Access Token 的 API
    sysdata.os = liff.getOS(); // String。回傳使用者作業系統：ios、android、web
    sysdata.lineVersion = liff.getLineVersion(); // 使用者的 LINE 版本
    console.log(sysdata.language,sysdata.version,sysdata.isInClient,sysdata.isLoggedIn,sysdata.os,sysdata.lineVersion);
    
  })
  .catch((err) => {
    console.log(err.code, err.message);
  });
})

const login = () => {
  if(!sysdata.isLoggedIn) {
    liff.login({
      redirectUri: window.location.href
    });
  }
}

const logout = () => {
  if(sysdata.isLoggedIn) {
    liff.logout();
    window.location.reload(); // 登出後重整一次頁面
  }
}

const getUserInfo = () => {
  if(sysdata.isLoggedIn) {
    // 取得使用者類型資料
    userdata.context = liff.getContext();
    console.log(userdata.context);

    // 取得使用者公開資料
    // 後台的「Scopes」要設定開啟 profile, openid
    liff.getProfile()
        .then(function(profile) {
          userdata.profile = profile;
          console.log(userdata.profile);
        });

    // 取得使用者 email
    // 後台的 Email address permission 要是「Applied」
    // LIFF 的設定，Scopes 的「email*」要打勾
    // 使用者在登入時，「電子郵件帳號」也要是「許可」的
    const user = liff.getDecodedIDToken();
    userdata.user = user
    console.log(userdata.user);
  }
}

const sendMessegeToSelf = () => {
  // 傳送訊息給自己
  // type 的可用值說明：https://developers.line.biz/en/reference/liff/#send-messages
  liff.sendMessages([
    {
      type: 'text',
      text: "嗨，測試訊息"
    }
  ])
  .then(res => window.alert(res.status))
  .catch(error => window.alert(error));
}

const sendMessegeToOther = () => {
  // 傳送訊息給朋友
  // 發訊息的可用參數：https://developers.line.biz/en/reference/liff/#share-target-picker
  if(isLoggedIn && liff.isApiAvailable('shareTargetPicker')) {
    liff.shareTargetPicker([
      {
        type: "text",
        text: "嗨，測試訊息"
      }
    ])
    .then(res => window.alert(res.status))
    .catch(error => window.alert(error))
  }
}

const openScanCode = () => {
  if(isLoggedIn && liff.scanCode) {
    liff.scanCode()
      .then(res => window.alert(res.status))
      .catch(error => window.alert(error))

  }
}
const closeAPP = () => {
  // 先確認是否在 LINE App 內
  if(isInClient) {
    liff.closeWindow();
  }
}
</script>

<template>
  <div>
    <div>
      <h1>系統資訊</h1>
      <p>系統語言：{{sysdata.language}}</p>
      <p>SDK版本：{{sysdata.version}}</p>
      <p>是否為line APP：{{sysdata.isInClient}}，版本為：{{sysdata.lineVersion}}</p>
      <p>是否登入狀態：{{sysdata.isLoggedIn}}</p>
      <p>平台：{{sysdata.os}}</p>
      </div>
    <div>
      這邊測試登入登出
      <br>
      <button @click="login()" class="py-2.5 px-5 mr-2 mb-2 text-sm font-medium text-gray-900 focus:outline-none bg-white rounded-lg border border-gray-200 hover:bg-gray-100 hover:text-blue-700 focus:z-10 focus:ring-4 focus:ring-gray-200 dark:focus:ring-gray-700 dark:bg-gray-800 dark:text-gray-400 dark:border-gray-600 dark:hover:text-white dark:hover:bg-gray-700">登入</button>
      <br>
      <button @click="logout()" class="py-2.5 px-5 mr-2 mb-2 text-sm font-medium text-gray-900 focus:outline-none bg-white rounded-lg border border-gray-200 hover:bg-gray-100 hover:text-blue-700 focus:z-10 focus:ring-4 focus:ring-gray-200 dark:focus:ring-gray-700 dark:bg-gray-800 dark:text-gray-400 dark:border-gray-600 dark:hover:text-white dark:hover:bg-gray-700">登出</button>  
    </div>
    <div>
      取得使用者資料
      <p>使用者類型：{{userdata.context}}</p>
      <p>使用者公開資料:{{userdata.profile}}</p>
      <p>{{userdata.user}}</p>
      <button @click="getUserInfo()" class="py-2.5 px-5 mr-2 mb-2 text-sm font-medium text-gray-900 focus:outline-none bg-white rounded-lg border border-gray-200 hover:bg-gray-100 hover:text-blue-700 focus:z-10 focus:ring-4 focus:ring-gray-200 dark:focus:ring-gray-700 dark:bg-gray-800 dark:text-gray-400 dark:border-gray-600 dark:hover:text-white dark:hover:bg-gray-700">取得使用者資料</button>  
    </div>
    <div>
      傳送訊息
      <button @click="sendMessegeToSelf()" class="py-2.5 px-5 mr-2 mb-2 text-sm font-medium text-gray-900 focus:outline-none bg-white rounded-lg border border-gray-200 hover:bg-gray-100 hover:text-blue-700 focus:z-10 focus:ring-4 focus:ring-gray-200 dark:focus:ring-gray-700 dark:bg-gray-800 dark:text-gray-400 dark:border-gray-600 dark:hover:text-white dark:hover:bg-gray-700">發送訊息給自己</button>  
      <button @click="sendMessegeToOther()" class="py-2.5 px-5 mr-2 mb-2 text-sm font-medium text-gray-900 focus:outline-none bg-white rounded-lg border border-gray-200 hover:bg-gray-100 hover:text-blue-700 focus:z-10 focus:ring-4 focus:ring-gray-200 dark:focus:ring-gray-700 dark:bg-gray-800 dark:text-gray-400 dark:border-gray-600 dark:hover:text-white dark:hover:bg-gray-700">發送訊息給別人</button>  
    </div>
    <div>
      打開QRcode
      <button @click="openScanCode()" class="py-2.5 px-5 mr-2 mb-2 text-sm font-medium text-gray-900 focus:outline-none bg-white rounded-lg border border-gray-200 hover:bg-gray-100 hover:text-blue-700 focus:z-10 focus:ring-4 focus:ring-gray-200 dark:focus:ring-gray-700 dark:bg-gray-800 dark:text-gray-400 dark:border-gray-600 dark:hover:text-white dark:hover:bg-gray-700">打開</button>  
    </div>
    <div>
      關閉
      <button @click="closeAPP()" class="py-2.5 px-5 mr-2 mb-2 text-sm font-medium text-gray-900 focus:outline-none bg-white rounded-lg border border-gray-200 hover:bg-gray-100 hover:text-blue-700 focus:z-10 focus:ring-4 focus:ring-gray-200 dark:focus:ring-gray-700 dark:bg-gray-800 dark:text-gray-400 dark:border-gray-600 dark:hover:text-white dark:hover:bg-gray-700">關閉</button>  
    </div>
  </div>
 
  
</template>
