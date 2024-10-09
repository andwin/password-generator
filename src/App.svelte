<script lang="ts">
  const uppercaseChars = "ABCDEFGHIJKLMNOPQRSTUVWXYZ"
  const lowercaseChars = "abcdefghijklmnopqrstuvwxyz"
  const numberChars = "0123456789"
  const symbolChars = "!@#$%^&*()_+~`|}{[]:;?><,./-="

  let includeUppercase: boolean
  let includeLowercase: boolean
  let includeNumbers: boolean
  let includeSymbols: boolean

  let length: number
  let password: string;

  let animatePasswordBorder = false;

  const generatePassword = () => {
    let chars = ''
    if (includeUppercase) chars += uppercaseChars
    if (includeLowercase) chars += lowercaseChars
    if (includeNumbers) chars += numberChars
    if (includeSymbols) chars += symbolChars

    const randomNumbers = new Uint32Array(length)
    self.crypto.getRandomValues(randomNumbers)
    password = Array.from(randomNumbers).map(randomNumber => {
      return chars[randomNumber % chars.length]
    }).join('')
  }

  const copyToClipboard = async () => {
    await navigator.clipboard.writeText(password);
    animatePasswordBorder = true
    setTimeout(() => animatePasswordBorder = false, 300)
  }

  const loadSettings = () => {
    const settings = localStorage.getItem('settings')
    if (!settings) return resestSettings()

    const parsedSettings = JSON.parse(settings)
    includeUppercase = parsedSettings.includeUppercase
    includeLowercase = parsedSettings.includeLowercase
    includeNumbers = parsedSettings.includeNumbers
    includeSymbols = parsedSettings.includeSymbols
    length = parsedSettings.length

    console.log('length', length)

  }

  const saveSettings = () => {
    const settings = {
      includeUppercase,
      includeLowercase,
      includeNumbers,
      includeSymbols,
      length
    }
    localStorage.setItem('settings', JSON.stringify(settings))
  }

  const resestSettings = () => {
    includeUppercase = true
    includeLowercase = true
    includeNumbers = true
    includeSymbols = true
    length = 25
    saveSettings()
    generatePassword()
  }

  const generatePasswordAndSaveSettings = () => {
    generatePassword()
    saveSettings()
  }

  loadSettings()
  generatePassword()
</script>

<main>
  <h1>Generate a password</h1>

  <form>
    <div class="password">
      <input type="text" name="password" id="password" value={password} class="{animatePasswordBorder ? 'animate-password-border' : ''}" />
    </div>

    <div class="buttons">
      <button type="button" on:click={generatePassword}>Generate</button>
      <button type="button" on:click={copyToClipboard}>Copy</button>
    </div>

    <div class="length">
      <label for="length">Password length</label>
      <input type="text" name="length" bind:value={length} on:input={generatePasswordAndSaveSettings} />
      <input type="range" min="1" max="50" bind:value={length} on:change={generatePasswordAndSaveSettings} class="slider" id="length-slider">
    </div>

    <div class="settings">
      <input type="checkbox" name="uppercase" id="uppercase" bind:checked={includeUppercase} on:change={generatePasswordAndSaveSettings} />
      <label for="uppercase">Uppercase</label>

      <input type="checkbox" name="lowercase" id="lowercase" bind:checked={includeLowercase} on:change={generatePasswordAndSaveSettings} />
      <label for="lowercase">Lowercase</label>

      <input type="checkbox" name="numbers" id="numbers" bind:checked={includeNumbers} on:change={generatePasswordAndSaveSettings} />
      <label for="numbers">Numbers</label>

      <input type="checkbox" name="symbols" id="symbols" bind:checked={includeSymbols} on:change={generatePasswordAndSaveSettings} />
      <label for="symbols">Symbols</label>
    </div>

    <div class="settings">
      <button type="button" on:click={resestSettings} class="reset-settings-btn">Reset settings</button>
    </div>
  </form>
</main>

<style>
  :global(html, body) {
    height: 100%;
    margin: 0;
    padding: 0;
    font-family: sans-serif;
    font-size: 24px;
    line-height: 1.5;
  }

  #password {
    font-size: 42px;
    font-weight: bold;
    text-align: center;
    padding: 4px 8px;
    margin-bottom: 24px;
    border-radius: 3px;
    width: 100%;
  }

  .animate-password-border {
    transition: border 0.1s ease-in-out;
    border: 2px solid #007bff;
  }

  .buttons {
    margin-bottom: 32px;
  }

  .reset-settings-btn {
    margin-top: 25px;
    font-size: 0.7em;
    background-color: #5c5c5c;
    padding: 0.5em 1em;
  }

  .length {
    margin-bottom: 32px;
  }
  .length label {
    display: block;
  }
  .length input[type="text"] {
    width: 60px;
    font-size: 32px;
    text-align: center;
  }
  .length input[type="range"] {
    width: 30%;
  }
</style>
