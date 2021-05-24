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
       <qrcode-vue size="200" level="M" :value="newQRCode"></qrcode-vue>
      </div>
      <br />
      <br />
       <div class="debugInfo">
        <h3>QR contents</h3>
        <textarea cols="25" rows="7" v-model="b64"></textarea>
        <pre>{{pretty}}</pre>
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
      showSettings : true,
      showQR:false,
      deviceId: "de",
      idScope: '',
      masterKey: '',
      payload : '{}',
      b64: ''
    }
  },
  created () {
        const savedSettings = JSON.parse(window.localStorage.getItem('dpsSettings') || '{}')
        if (savedSettings.idScope)
        {
            this.idScope = savedSettings.idScope
            this.masterKey = savedSettings.masterKey
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
            this.deviceId = 'dev-' + Date.now()
            this.showSettings = false
            this.showQR = true

        }
    },
  components: {
   //  HelloWorld,
    QrcodeVue
  },
  asyncComputed: {
    newQRCode() {
            const ScopeId = this.idScope
            const DeviceId = this.deviceId
            createHmac(this.masterKey, this.deviceId)
                .then( DeviceKey => {
                    this.payload = JSON.stringify({ScopeId,DeviceId,DeviceKey})
                    this.b64 = btoa(this.payload)
            })
      return this.b64
    },
    pretty () {
      return JSON.stringify(JSON.parse(this.payload), null, 2)
    }
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
div {
  margin: 5px;
}
.qrCode {
  margin-top:20px;
}
.debugInfo {
  margin-top:10px;
  padding:5px;
  background-color:silver;
}
pre {
  text-align:left;
}
</style>
