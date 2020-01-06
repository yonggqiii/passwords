<template>
  <div class="container">
    <div :class="{'hidden': !appear}" class="popup">
      <div class="toast">
        Copied to clipboard!
      </div>
    </div>
    <div>
      <h1 class="title">
        passwords
      </h1>
      <p style="font-size: 2vw; margin: 0 0 30px 0;">
        Developed using Nuxt.js
      </p>
      <h2 class="subtitle">
        A simple password generator
      </h2>
      <div>
        <input v-model="master" @input="handleInput" :type="masterHidden ? 'password' : 'text'" style="font-size: 16px;">
      </div>

      <button @click="masterHidden = !masterHidden" class="showButton">
        Show Entry
      </button>

      <div style="max-width: 90vw; margin: 10px 0; padding: 3px; border: white 3px solid; border-radius: 4px;">
        <pre>{{ resultHidden ? '************************************************************' : result }}</pre>
      </div>
      <button @click="resultHidden = !resultHidden" class="showButton">
        Show Result
      </button>
      <button @click="copy" class="copy">
        Copy to clipboard
      </button>
    </div>
  </div>
</template>

<script>
export default {

  data () {
    return {
      master: '',
      result: '',
      masterHidden: true,
      resultHidden: true,
      appear: false
    }
  },
  mounted () {
    this.handleInput()
  },

  methods: {
    copy () {
      const el = document.createElement('textarea')
      el.value = this.result
      document.body.appendChild(el)
      el.select()
      document.execCommand('copy')
      document.body.removeChild(el)
      this.appear = true
      setTimeout(() => {
        this.appear = false
      }, 2000)
    },
    async handleInput () {
      const iterate = async function (str) {
        async function sha512 (str) {
          const buf = await crypto.subtle.digest('SHA-512', new TextEncoder('utf-8').encode(str))
          return fromByteArray(new Uint8Array(buf), false)
        }
        const LINE_WIDTH = 80
        function getEncodedChunk (tuple, bytes = 4) {
          let output
          let d = ((tuple[0] << 24) | (tuple[1] << 16) | (tuple[2] << 8) | tuple[3]) >>> 0
          if (d === 0 && bytes === 4) {
            output = new Uint8Array(1)
            output[0] = 0x7A // z
          } else {
            output = new Uint8Array(bytes + 1)
            for (let i = 4; i >= 0; i--) {
              if (i <= bytes) {
                output[i] = d % 85 + 0x21 // 0x21 = '!'
              }
              d /= 85
            }
          }
          return output
        }
        function fromByteArray (byteArray, useEOD = true) {
          const output = []
          let lineCounter = 0
          if (useEOD) {
            output.push(0x3C) // <
            output.push(0x7E) // ~
          }
          for (let i = 0; i < byteArray.length; i += 4) {
            const tuple = new Uint8Array(4)
            let bytes = 4
            for (let j = 0; j < 4; j++) {
              if (i + j < byteArray.length) {
                tuple[j] = byteArray[i + j]
              } else {
                tuple[j] = 0x00
                bytes--
              }
            }
            const chunk = getEncodedChunk(tuple, bytes)
            for (let j = 0; j < chunk.length; j++) {
              if (lineCounter >= LINE_WIDTH) {
                output.push(0x0D) // \n
                lineCounter = 0
              }
              output.push(chunk[j])
              lineCounter++
            }
          }
          if (useEOD) {
            output.push(0x7E) // ~
            output.push(0x3E) // >
          }
          return String.fromCharCode.apply(null, output)
        }
        const a = await sha512(str)
        return a
      }
      this.result = (await iterate(this.master)).substring(0, 60)
    }
  }
}
</script>

<style>
.toast {
  padding: 20px;
  font-size: 16px;
  background: rgb(0, 190, 190);
  color: white;
  border-radius: 10px;
}
.popup {
  position: fixed;
  bottom: 5vh;
  left: 0;
  width: 100vw;
  display: flex;
  align-items: center;
  opacity: 1;
  transition: opacity 400ms ease-in-out;
  justify-content: center;
}
.popup.hidden {
  opacity: 0;
}
.showButton {
  color: #00c58e;
  border: #00c58e 3px solid;
  border-radius: 7px;
}
.showButton:hover {
  color: #00fdb5;
  border: #00fdb5 3px solid;
}
.showButton:active {
  background: #00412e;
  color: #00fdb5;
  border: #00fdb5 3px solid;
}
.copy {
  color: #00bcdd;
  border: #00bcdd 3px solid;
  border-radius: 7px;
}
.copy:active {
  color: #1fddff;
  border: #1fddff 3px solid;
  background:#003842;
  border-radius: 7px;
}
.copy:hover {
  color: #1fddff;
  border: #1fddff 3px solid;
  border-radius: 7px;
}
button {
  font-size: 16px;
  margin: 1vh;
  padding: 8px;
  font-family: Roboto;
  background: none;
  border: none;
  outline: none;
}
pre {
      white-space: -moz-pre-wrap !important;  /* Mozilla, since 1999 */
    white-space: -pre-wrap;      /* Opera 4-6 */
    white-space: -o-pre-wrap;    /* Opera 7 */
    white-space: pre-wrap;       /* css-3 */
    word-wrap: break-word;       /* Internet Explorer 5.5+ */
    white-space: -webkit-pre-wrap; /* Newer versions of Chrome/Safari*/
    word-break: break-all;
    white-space: normal;
}
input {
  background: black;
  color: white;
  border: #00c58e 3px solid;
  border-radius: 3px;
  padding: 10px;
}
input:focus {
  outline: 0;
}
.container {
  background: black;
  color: white;
  margin: 0 auto;
  height: 100vh;
  width: 100vw;
  display: flex;
  justify-content: center;
  align-items: center;
  text-align: center;
}

.title {
  font-family: 'Quicksand', 'Source Sans Pro', -apple-system, BlinkMacSystemFont,
    'Segoe UI', Roboto, 'Helvetica Neue', Arial, sans-serif;
  display: block;
  font-weight: 300;
  font-size: 10vw;
  color: #00c58e;
  letter-spacing: 1px;
}

.subtitle {
  font-weight: 300;
  font-size: 4vw;
  color: #108775;
  word-spacing: 0.5vw;
  padding-bottom: 15px;
}

.links {
  padding-top: 15px;
}
</style>
