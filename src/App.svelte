<script lang="ts">
  const uppercaseChars = "ABCDEFGHIJKLMNOPQRSTUVWXYZ"
  const lowercaseChars = "abcdefghijklmnopqrstuvwxyz"
  const numberChars = "0123456789"
  const symbolChars = "!@#$%^&*()_+~`|}{[]:;?><,./-="

  let includeUppercase = true
  let includeLowercase = true
  let includeNumbers = true
  let includeSymbols = true

  let length = 25
  let password: string;

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
  }

  generatePassword()
</script>

<main>
  <h1>Generate a password</h1>

  <form>
    <div class="password">
      <input type="text" name="password" id="password" value={password} />
    </div>

    <div class="buttons">
      <button type="button" on:click={generatePassword}>Generate</button>
      <button type="button" on:click={copyToClipboard}>Copy</button>
    </div>

    <div class="length">
      <label for="length">Password length</label>
      <input type="text" name="length" bind:value={length} on:input={generatePassword} />
      <input type="range" min="1" max="50" bind:value={length} on:change={generatePassword} class="slider" id="length-slider">
    </div>

    <div class="settings">
      <input type="checkbox" name="uppercase" id="uppercase" bind:checked={includeUppercase} on:change={generatePassword} />
      <label for="uppercase">Uppercase</label>

      <input type="checkbox" name="lowercase" id="lowercase" bind:checked={includeLowercase} on:change={generatePassword} />
      <label for="lowercase">Lowercase</label>

      <input type="checkbox" name="numbers" id="numbers" bind:checked={includeNumbers} on:change={generatePassword} />
      <label for="numbers">Numbers</label>

      <input type="checkbox" name="symbols" id="symbols" bind:checked={includeSymbols} on:change={generatePassword} />
      <label for="symbols">Symbols</label>
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

  .buttons {
    margin-bottom: 32px;
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
