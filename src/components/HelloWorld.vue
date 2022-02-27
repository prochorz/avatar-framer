<script setup>
import {ref, onMounted, reactive, defineExpose, watch} from 'vue';
import Dropzone from 'dropzone';
import {fabric} from 'fabric';
import Social from './social/social.vue';

Dropzone.autoDiscover = false;

// Change the canvas frames
// one per frame
// add your frames here
const images = Array(14).fill(null).map((_, index) => 'frame-' + (index + 1));

function getImageUrl(name) {
  if (import.meta.url) {
    return new URL(`/${name}.png`, import.meta.url).href
  }

  return `./${name}.png`;
}

const canvasRef = ref();
const isImageExist = ref(false);
const inputs = reactive({
  angle: 0,
  scale: 50
});

// image to be added to the canvas
var image = new fabric.Image();

function updateControls() {
  inputs.scale = image.scaleX;
  inputs.angle = image.angle;
}

function run() {
  // define the canvas
  canvasRef.value = new fabric.Canvas('canvas-id', {
    width: 500,
    height: 500
  });

  // declare the dropzone
  var imgUpload = new Dropzone("#upload", {
    url: "UploadImages",
    autoProcessQueue: false,
    createImageThumbnails: false,
    maxFiles: 1,
    acceptedFiles: "image/*",
    addRemoveLinks: false,
    clickable: '#upload'
  });

  fabric.Object.prototype.transparentCorners = false;

  canvasRef.value.on({
    'object:moving': updateControls,
    'object:scaling': updateControls,
    'object:resizing': updateControls,
    'object:rotating': updateControls
  });

  canvasRef.value.setOverlayImage(getImageUrl('frames/' + images[0]), function () {
    canvasRef.value.overlayImage.scaleToWidth(canvasRef.value.getWidth())
    canvasRef.value.renderAll()
  }, {
    originX: 'left',
    originY: 'top',
    crossOrigin: 'anonymous'
  });

  var reader = new FileReader();
  reader.onload = function (event) {

    var imgObj = new Image();
    imgObj.src = event.target.result;

    fabric.util.loadImage(imgObj.src, function () {
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
        hasControls: false
      });

      image.scaleToHeight(canvasRef.value.getHeight());
      image.scaleToWidth(canvasRef.value.getWidth());
      canvasRef.value.centerObject(image);
      canvasRef.value.add(image);
      canvasRef.value.renderAll();
      canvasRef.value.setActiveObject(image);
    });
  };

  // when the image is uplaoded
  // call the reader to add it to the canvas
  // enable the download button and controls
  // remove the upload text
  imgUpload.on("addedfile", function (file) {
    isImageExist.value = true;
    reader.readAsDataURL(file);
  });

  // when the image is removed
  // remove it from the canvas
  // disable the download button and controls
  // re-add the upload text
  imgUpload.on("removedfile", function () {
    isImageExist.value = false;
    // canvasRef.value.remove(image);
  });
}

watch(() => inputs.scale, (value) => {
  image.scale(parseFloat(value) / 200).setCoords();
  canvasRef.value.requestRenderAll();
});

watch(() => inputs.angle, (value) => {
  image.set('angle', parseInt(value, 10)).setCoords();
  canvasRef.value.requestRenderAll();
});

// handle download
// create a link and simulate a click to download the file
function downloadFile() {
  const link = canvasRef.value.toDataURL({format: 'jpeg', quality: 1, multiplier: 4});
  const linkNode = document.createElement('a');
  linkNode.href = link;
  linkNode.download = 'profile-pic.jpeg';
  document.body.appendChild(linkNode);
  linkNode.click();
  document.body.removeChild(linkNode);
}

function resizeCanvas() {
  // fabric.js wraps the canvas in a ".canvas-container" div
  // manually set the container div's height to it's width
  // this will not affect the canvas size for download

  const outerCanvasContainer = document.getElementsByClassName("canvas-container")[0];
  outerCanvasContainer.style.width = "auto";
  outerCanvasContainer.style.height = `${outerCanvasContainer.clientWidth}px`;
}

function changeFrame(image) {
  canvasRef.value.setOverlayImage(image, function () {
    canvasRef.value.overlayImage.scaleToWidth(canvasRef.value.getWidth())
    canvasRef.value.renderAll()
  }, {
    originX: 'left',
    originY: 'top',
    crossOrigin: 'anonymous'
  });
}

function addFile() {

}

onMounted(run);

// resize the canvas once it's loaded
onMounted(resizeCanvas);

window.addEventListener('resize', resizeCanvas);

defineExpose({
  images,
  inputs,
  isImageExist,
  getImageUrl,
  changeFrame,
  downloadFile
});

</script>

<template>
  <div class="wrapper">
    <div class="main__header">
      <p class="main__hash">#МОЛЧАНИЕ<span>УБИВАЕТ</span></p>
      <h1 class="main__title">Давайте остановим войну</h1>
      <p class="main__slogan">
        <strong>Ваше молчание убивает людей 🇺🇦 и 🇷🇺.</strong> Любая огласка сейчас поможет прекратить это всё.
        Сделайте свой вклад в мир во всем мире!
      </p>
    </div>
    <div class="main__steps">
      <div class="main__step main__step--1">
        <div class="main__step___message">
          <span class="main__step__count">1</span>
          <div class="main__step__text">Вставьте ссылку на свою соц.сеть Сюда загрузится ваш аватар</div>
        </div>
        <div class="main__step__input">
          <button
              id="upload"
              class="main__step__btn"
              @click="addFile"
          >
            Загрузить
          </button>
        </div>
      </div>
      <div class="main__step">
        <div class="main__step___message">
          <span class="main__step__count">2</span>
          <div class="main__step__text">Выберите маску для аватара из предложенных ниже</div>
        </div>
        <div class="main__step__builder">
          <div class="main__step__builder__ava">
            <canvas
                id="canvas-id"
                class="main__step__builder__ava-canvas"
            />

            <div class="main__step__builder__ava-controls">
              <div class="pt-6--rotation">
                <label for="angle-control">Rotation:</label>
                <input
                    v-model="inputs.angle"
                    type="range"
                    id="angle-control"
                    min="0"
                    max="360"
                    class="w-full"
                    :disabled="!isImageExist"
                >
              </div>
              <div>
                <label for="scale-control">Scale:</label>
                <input
                    v-model="inputs.scale"
                    type="range"
                    id="scale-control"
                    min="1"
                    max="100"
                    :disabled="!isImageExist"
                >
              </div>
            </div>
          </div>
          <div class="main__mask_buttons">
            <button
                v-for="item in images"
                @click="changeFrame(getImageUrl(`frames/`+ item))"
            >
              <img :src="getImageUrl(`previews/`+ item)">
            </button>
          </div>
        </div>

      </div>

      <div class="main__step main__step--1">
        <div class="main__step___message">
          <span class="main__step__count">3</span>
          <div class="main__step__text">Скачайте картинку и установите на аватарку</div>
        </div>
        <div class="main__step__input">
          <button
              :disabled="!isImageExist"
              @click="downloadFile"
          >
            Скачать
          </button>
          </div>
      </div>
    </div>
    <social />
  </div>
</template>

<style />
