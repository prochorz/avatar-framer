<script setup>
import { ref, onMounted, defineExpose } from 'vue';
import Dropzone from 'dropzone';
import { fabric } from 'fabric';

const images = ['frame-1', 'frame-2', 'frame-3'];

function getImageUrl(name) {
  return new URL(`/${name}.png`, import.meta.url).href
}

const canvasRef = ref();

function run() {
  Dropzone.autoDiscover = false;

  document.addEventListener("DOMContentLoaded", function(){

    // define the canvas
    const canvas = new fabric.Canvas('c', {
      width: 500,
      height: 500
    });

    canvasRef.value = canvas;

    // image to be added to the canvas
    var image = new fabric.Image();

    // resize the canvas once it's loaded
    resizeCanvas();

    // declare the dropzone
    var imgUpload = new Dropzone("#upload", {
      url: "UploadImages",
      autoProcessQueue: false,
      createImageThumbnails: false,
      maxFiles: 1,
      acceptedFiles: "image/*",
      addRemoveLinks: true,
      clickable: '#upload, #upload-text'
    });

    // handle the canvas controls and overlay
    (function() {
      fabric.Object.prototype.transparentCorners = false;

      var $ = function(id){return document.getElementById(id)};

      var angleControl = $('angle-control');
      angleControl.oninput = function() {
        image.set('angle', parseInt(this.value, 10)).setCoords();
        canvas.requestRenderAll();
      };

      var scaleControl = $('scale-control');
      scaleControl.oninput = function() {
        image.scale(parseFloat(this.value)/200).setCoords();
        canvas.requestRenderAll();
      };

      function updateControls() {
        scaleControl.value = image.scaleX;
        angleControl.value = image.angle;
      }
      canvas.on({
        'object:moving': updateControls,
        'object:scaling': updateControls,
        'object:resizing': updateControls,
        'object:rotating': updateControls,
      });

      canvas.setOverlayImage(getImageUrl('frames/' + images[0]) , function() {
        canvas.overlayImage.scaleToWidth(canvas.getWidth())
        canvas.renderAll()
      }, {
        originX: 'left',
        originY: 'top',
        crossOrigin: 'anonymous'
      });

    })();

    var reader = new FileReader();
    reader.onload = function(event) {

      var imgObj = new Image();
      imgObj.src = event.target.result;

      fabric.util.loadImage(imgObj.src, function() {
        image = new fabric.Image(imgObj);
        image.set({
          lockMovementX: false,
          lockMovementY: false,
          lockScalingX: false,
          lockScalingY: false,
          lockRotation: false,
          selectable: true,
          centeredRotation: true,
          centeredScaling: true,
          cornerColor: "#0d2240",
          cornerSize: 30,
          lockSkewingX: true,
          lockSkewingY: true,
          originX: "center",
          originY: "center",
          hasControls: false,
        });

        image.scaleToHeight(canvas.getHeight());
        image.scaleToWidth(canvas.getWidth());
        canvas.centerObject(image);
        canvas.add(image);
        canvas.renderAll();
        canvas.setActiveObject(image);
      });
    };

    // when the image is uplaoded
    // call the reader to add it to the canvas
    // enable the download button and controls
    // remove the upload text
    imgUpload.on("addedfile", function(file) {
      reader.readAsDataURL(file);
      document.getElementById("download").disabled = false;
      document.getElementById("angle-control").disabled = false;
      document.getElementById("scale-control").disabled = false;
      document.getElementById("upload-text").innerHTML = "";
    });

    // when the image is removed
    // remove it from the canvas
    // disable the download button and controls
    // re-add the upload text
    imgUpload.on("removedfile", function() {
      canvas.remove(image);
      document.getElementById("download").disabled = true;
      document.getElementById("angle-control").disabled = true;
      document.getElementById("scale-control").disabled = true;
      document.getElementById("upload-text").innerHTML = "Drop files here or click to upload.";
    });

    // Change the canvas frames
    // one per frame
    // add your frames here

    const frames = ['frame-1.png', 'frame-2.png', 'frame-3.png'];

    // {% for image in site.static_files %}
    // {% if image.path contains 'frames/' %}
    // document.getElementById("{{ image.basename }}").addEventListener("click", function (){
    //   changeFrame(canvas, '{{ image.path | relative_url }}');
    // });
    // {% endif %}
    // {% endfor %}

    // handle download
    // create a link and simulate a click to download the file
    var download = document.getElementById("download");
    download.addEventListener('click', function() {
      var e = canvas.toDataURL({ format: "jpeg", quality: 1, multiplier: 4});
      if (window.URL && e) {
        if (window.navigator.msSaveOrOpenBlob) window.navigator.msSaveOrOpenBlob(t, "profile-pic.jpeg");
        else {
          var r = document.createElement("a");
          (r.href = e), (r.download = "profile-pic.jpeg"), document.body.appendChild(r), r.click(), document.body.removeChild(r);
        }
      }
    }, false);
  });

  window.addEventListener('resize', function() {
    resizeCanvas();
  });
}

function resizeCanvas() {
  // fabric.js wraps the canvas in a ".canvas-container" div
  // manually set the container div's height to it's width
  // this will not affect the canvas size for download

  const outerCanvasContainer = document.getElementsByClassName("canvas-container")[0];
  outerCanvasContainer.style.width = "auto";
  outerCanvasContainer.style.height = `${outerCanvasContainer.clientWidth}px`;
}

function changeFrame(canvas, image) {
  canvas.setOverlayImage(image, function() {
    canvas.overlayImage.scaleToWidth(canvas.getWidth())
    canvas.renderAll()
  }, {
    originX: 'left',
    originY: 'top',
    crossOrigin: 'anonymous'
  });
}

function onChangeFrameHandler(imgUrl) {
  changeFrame(canvasRef.value, imgUrl);
}

onMounted(run);

defineExpose({
  images,
  getImageUrl,
  onChangeFrameHandler
});

</script>

<template>
  <div>
    <div>
      <h1 class="font-bold text-3xl leading-8">
        {{ 'site.title-text' }}
      </h1>

      <ol class="instructions mt-10">
        <li class="text-lg leading-5 font-medium">
          {{ 'site.step-1' }}

          <div id="upload" class="mt-4 p-6 w-full bg-gray-100 text-center rounded cursor-pointer hover:text-secondary hover:underline transition duration-500 ease-in-out">
            <span id="upload-text">{{ 'site.upload-text' }}</span>
          </div>


          <!-- <form action="/file-upload" class="dropzone" id="uploadImg"></form> -->
        </li>

        <li class="text-lg leading-5 font-medium pt-12">
          {{ 'site.step-2' }}
        </li>

        <li class="text-lg leading-5 font-medium pt-12">
          {{ 'site.step-3' }}

          <div class="mt-4 grid grid-cols-3 gap-4">
            <img
                v-for="item in images"
                :id="`frame-` + item"
                class="object-contain w-full max-w-6 rounded-full border-2 border-gray-100 inline-block shadow-xl transition duration-300 ease-in-out cursor-pointer hover:border-primary"
                :src="getImageUrl(`previews/`+ item)"
                alt=""
                @click="onChangeFrameHandler(getImageUrl(`frames/`+ item))"
            >
          </div>
        </li>

        <li class="text-lg leading-5 font-medium pt-12">
          {{ 'site.step-4' }}

          <button class="block max-w-56 mt-4 px-8 py-5 rounded bg-primary text-white text-sm font-bold text-center" id="download" disabled>
            {{ 'site.download-button-text' }}
          </button>
        </li>

        <li class="text-lg leading-5 font-medium pt-12">
          {{ 'site.step-5' }}
        </li>
      </ol>
    </div>
  </div>


  <div class="mt-12 md:mt-0">
    <canvas id="c" style="border: 1px solid grey; " ></canvas>

    <div class="controls w-full">
      <div class="pt-6">
        <label for="angle-control" class="text-lg font-medium leading-5">Rotation:</label>
        <input type="range" id="angle-control" value="0" min="0" max="360" class="w-full" disabled>
      </div>
      <div class="pt-6">
        <label for="scale-control" class="text-lg font-medium leading-5">Scale:</label>
        <input type="range" id="scale-control" value="50" min="1" max="100" class="w-full" disabled>
      </div>
    </div>
  </div>
</template>

<style scoped>
a {
  color: #42b983;
}
</style>
