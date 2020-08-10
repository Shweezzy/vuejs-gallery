<template>
  <div id="app">
    <HelloWorld />
  </div>
</template>

<script>
import HelloWorld from "./components/HelloWorld.vue";

export default {
  name: "App",
  components: {
    HelloWorld,
  },
};
</script>

<style>
#googlePhoto {
  max-width: 230px;
  width: 100%;
}
#switchSpan {
  color: #007bff;
  vertical-align: middle;
}
input[type="file"] {
  display: none;
}
#addBtn {
  border: none;
}
#switch {
  float: inherit;
  width: 90px;
}
.row {
  margin: 0;
}
.col-sm {
  text-align: center;
}
.col-sm-6 {
  text-align: center;
}
.btn-secondary {
  opacity: 0.6;
}
.fileupload {
  padding: 0.4em 1em 0.4em 1em;
  border-radius: 0.3em;
  margin: 0;
  color: #007bff;
}
#cardText {
  text-align: center;
  opacity: 0.6;
  padding-bottom: 5%;
}
.fileupload:hover {
  cursor: pointer;
  color: white;
  background-color: #007bff;
}
.custom-file-upload {
  display: inline-block;
  padding: 6px 12px;
  cursor: pointer;
}
.form-inline {
  width: 100%;
}
#__BVID__5 {
  width: 100%;
}
#file-selector__BV_file_outer_ {
  width: 90%;
}
#carousel-1 {
  width: 600px;
}
#carousel-image {
  height: 500px;
}
#carousel {
  display: flex;
  justify-content: center;
}
.card-body {
  padding: 0;
}
</style>
