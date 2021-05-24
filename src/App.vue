<template>
  <div id="app">
   <div id="dpsSettings" v-show="showSettings">
      <div>
        <label for="idScope">ID Scope</label>
        <input type="text" size="25"  v-model="idScope">
      </div>
      <div>
        <label for="masterKey">Master Key</label>
        <input type="text" size="25" v-model="masterKey">
      </div>
      <div>
        <button @click="saveDpsSettings()">Save</button>
      </div>
    </div>

     <div id="deviceQR" class="output" v-show="showQR">
      <button v-show="showQR" @click="showSettingsButton()">DPS Settings</button>
      <div>
        <label for="deviceID">Device Id</label>
        <input type="text" size="25" v-model="deviceId" />
      </div>
      <div class="qrCode">
       <qrcode-vue size ="200" :value="newQRCode"></qrcode-vue>
      </div>
      <br />
      <br />
       <div>
        <textarea cols="65" rows="2">{{b64}}</textarea>
        <pre>{{payload}}</pre>
       </div>
    </div>
  </div>
   
   </div>
</template>

<script>
// import HelloWorld from './components/HelloWorld.vue'
import QrcodeVue from 'qrcode.vue'

const createHmac = async (key, msg) => {
    const keyBytes = Uint8Array.from(window.atob(key), c => c.charCodeAt(0))
    const msgBytes = Uint8Array.from(msg, c => c.charCodeAt(0))
    const cryptoKey = await window.crypto.subtle.importKey(
        'raw', keyBytes, { name: 'HMAC', hash: 'SHA-256' },
        true, ['sign']
    )
    const signature = await window.crypto.subtle.sign('HMAC', cryptoKey, msgBytes)
    return window.btoa(String.fromCharCode(...new Uint8Array(signature)))
}

export default {
  name: 'App',
  data() {
    return {
      value : 'asdfasdfasdfasdfsdfa',
      showSettings : true,
      showQR:false,
      deviceId: "de",
      idScope: '0ne002CD355',
      masterKey: 'xPWEtZjR5kFcYxSm0l35DzgfAWb44aXHvGKfTDQMB8cuyid7NZofOJB6OYxytahunq6suar2sXRRgmKlkPeKjQ==',
      payload : '',
      b64: ''
    }
  },
  methods: {
        showSettingsButton () {
            this.showSettings = true,
            this.showQR = false
        },
        saveDpsSettings () {
            window.localStorage.setItem('dpsSettings',
                JSON.stringify({
                    idScope: this.idScope,
                    masterKey: this.masterKey
                })
            )
            this.showSettings = false
            this.showQR = true
        }
    },
  components: {
   //  HelloWorld,
    QrcodeVue
  },
  computed: {
    newQRCode() {
            const ScopeId = this.idScope
            const DeviceId = this.deviceId
            createHmac(this.masterKey, this.deviceId)
                .then( DeviceKey => {
                    this.payload = JSON.stringify({ScopeId,DeviceId,DeviceKey})
                    this.b64 = btoa(this.payload)
                    this.payload = this.payload;
            })
          return this.payload
        },
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
  margin:10px
}
.qrCode {
  margin:20px;
  padding:20px;
  border: 2px solid grey;
}

</style>
