<script>
	/**
	 * @type {null}
	 */
	let imageSrc = null;
	let completion = '';
	let inputValue = '';


  import { ProgressBar } from '@skeletonlabs/skeleton';
	import { LightSwitch } from '@skeletonlabs/skeleton';
  import 'remixicon/fonts/remixicon.css'

  // Convert blob to Base64 data URL
  async function blobToDataURL(/** @type {Blob} */ blob) {
    return new Promise((resolve) => {
      const reader = new FileReader();
      reader.onloadend = () => resolve(reader.result);
      reader.readAsDataURL(blob);
    });
  }

  // Display the blob as an image
  async function displayBlobAsImage(/** @type {Blob} */ blob) {
    const dataURL = await blobToDataURL(blob);
    console.log('Image URL:', dataURL);
    imageSrc = dataURL;
  }

    // Call the My HuggingFace API
    async function query(/** @type {{ inputs: string; }} */ data) {
        const response = await fetch('/api/huggingface', {
            method: 'POST',
            headers: {
                'Content-Type': 'application/json',
            },
            body: JSON.stringify(data),
            });
    const result = await response.blob();
    return result;
  }


  async function query2(/** @type {string} */ data) { // Fixed here
    const response = await fetch('/api/openai', {
      method: 'POST',
      body: JSON.stringify({ prompt: data }),
      headers: {
        'content-type': 'application/json'
      }
    });

    completion = await response.text()
    console.log('comp: ', completion);
    return completion;
  }





    // Handle button click
    const callOpenAI = () => {
      query2("only reply with a one word answer. Name a single material used in " + inputValue)
      .then((responseFromQuery2) => {
          console.log('OpenAI Response:', responseFromQuery2);
          return query({ "inputs": "pbr " + responseFromQuery2 });
      })
      .then((responseFromQuery) => {
          displayBlobAsImage(responseFromQuery);
      })
      .catch((error) => {
          console.error('Error:', error);
      });
  }

  </script>



<div>
  <div>
	<h1>Texsture Image Generator</h1>
  <LightSwitch />
</div>




<div class="card p-4">
  <aside class="alert variant-filled-warning">
    <!-- Icon -->
    <div><i class="ri-alarm-warning-fill"></i></div>
    <!-- Message -->
    <div class="alert-message">
      <h3 class="h3">Alert</h3>
      <p>This website is early access so it is very likely to break.
        - if you type want you want to see and no image apears you can wait a couple seconds and then click genarate agnin and see if it works.</p>
    </div>
    <!-- Actions -->
    <div class="alert-actions"></div>
  </aside>

	<h2 class="h2">How my program works:</h2>
	<p>Type in a location or building and ChatGPT will come up with a material that would be used in that location and it should be shown bellow</p>
	<h2 class="h2">Tip:</h2>
	<p>Type "House" and chatGPT should come up with a martile such as wood and then it should be displayed bellow</p>
</div>


<div class="card p-4">
<button type="button" class="btn variant-filled">
	<span><i class="ri-magic-fill"></i></span>
	<span>Button</span>
</button>
<ProgressBar />

	<input type="text" bind:value={inputValue} placeholder="Enter text here"/>
	<button on:click={callOpenAI}>Generate</button>
	{#if imageSrc}
	  <img src={imageSrc} alt="Generated" />
	{/if}
</div>
  </div>
