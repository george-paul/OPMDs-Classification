
<script>
// TODO: Make responsive


// @ts-nocheck
  import { Camera, Photo, XCircle } from "svelte-heros";

  function sleep(ms) {
    return new Promise(resolve => setTimeout(resolve, ms));
  }

	let previewImg, fileinput;
	let imageFile;
  const onFileSelected =(e)=>{
    imageFile = e.target.files[0];
    let reader = new FileReader();
    reader.readAsDataURL(imageFile);
    reader.onload = e => {
      previewImg = e.target.result
    };
  }

  let isLoading;
  let classified;
  
  const unselectFile = () => {
    previewImg = null;
    isLoading = false;
    classified = false;
  }

  function offsetToIndia(date) {
    date.setMinutes((date.getMinutes() + 30) % 60);
    date.setHours((date.getHours() + 6) % 60);
    return date
  }

  const classify = async () => {
    isLoading = true;

    const fd = new FormData();
    // image data
    fd.append('image', imageFile);
    // path to save the image at in minio
    let date = new Date();
    let convDate = offsetToIndia(date)
    let timestring = convDate.toISOString().substring(0,19)
    timestring = timestring.replace(/\D/g, "")
    console.log(timestring)

    fd.append('path', "webapp_"+timestring+".jpg")

    // Send a POST request
    fetch('https://healthcare-data.iiit.ac.in/inferencedev/upload', {
      method: 'POST',
      body: fd
    })
    .then( async res => {
      label = await res.text();
      isLoading = false;
      classified = true;
    })
    // .then(json => console.log(json))
    .catch(err => console.error(err));
  }

  let label = "";
</script>


{#if previewImg}
  <!-- ---------------------- Image Uploaded ---------------------- -->
  <div class="grow h-[30%] relative">
    <img class="object-cover rounded-[2rem] h-full w-full p-5" src="{previewImg}" alt="Uploaded"/>
    <div class="top-8 right-8 absolute bg-black rounded-[50%] blur-[8px] w-4 h-4">
    </div>
    <XCircle size="30" class="top-6 right-6 absolute cursor-pointer text-scd-50 rounded-[50%]" on:click={(e) => unselectFile()}/>
  </div>
  <div class="grow">
    <div class="flex flex-row items-center justify-center">
      <button class="dark:bg-acc-900 dark:bg-opacity-50 bg-acc-300 bg-opacity-50 dark:text-scd-50 p-2 rounded-xl font-bold" on:click={(e) => classify()}>
        Classify
      </button>
    </div>
    <div class="flex flex-row items-center justify-center p-5">
      {#if isLoading}
        <div class="w-6 h-6 border-2 border-dashed rounded-full animate-spin dark:border-scd-50 border-prm-800"></div>
      {:else if classified}
        <p class="dark:text-scd-50">The image is: <b class="font-mono">{label}</b></p>
      {:else}
        <div class="min-h-6 h-6">
        </div>
      {/if}
    </div>
  </div>
{:else}
  <!-- ---------------------- Camera/Gallery Screen ---------------------- -->
  <div class="">
    <p class="text-center text-2xl dark:text-scd-50 pt-10">
      Upload a Picture:
    </p>
    <div class = "flex flex-row pt-10 justify-evenly">
      <!-- <div class="flex flex-col items-center dark:bg-acc-900 dark:bg-opacity-50 bg-acc-300 bg-opacity-50 p-4 rounded-3xl">
        <Camera class=" dark:text-scd-50 scale-150 pt-1"/>
        <span class="dark:text-scd-50 pt-2">
          Camera
        </span>
      </div> -->
      <button on:click={()=>{fileinput.click();}} class="flex flex-col items-center dark:bg-acc-900 dark:bg-opacity-50 bg-acc-300 bg-opacity-50 p-4 rounded-3xl">
        <input style="display:none" type="file" accept=".jpg, .jpeg, .png" capture="environment" on:change={(e)=>onFileSelected(e)} bind:this={fileinput}>
        <Camera class=" dark:text-scd-50 scale-150 pt-1"/>
        <span class="dark:text-scd-50 pt-2">
          Camera
        </span>
      </button>
      <!-- svelte-ignore a11y-click-events-have-key-events -->
      <button on:click={()=>{fileinput.click();}} class="flex flex-col items-center dark:bg-acc-900 dark:bg-opacity-50 bg-acc-300 bg-opacity-50 p-4 rounded-3xl">
        <input style="display:none" type="file" accept=".jpg, .jpeg, .png" on:change={(e)=>onFileSelected(e)} bind:this={fileinput}>
        <Photo class=" dark:text-scd-50 scale-150 pt-1"/>
        <span class="dark:text-scd-50 pt-2">
          Gallery
        </span>
      </button>
    </div>
  </div>

{/if}