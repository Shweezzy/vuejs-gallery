<template>
  <div>
    <div class="row align-items-center">
      <div class="col-sm">
        <img
          id="googlePhoto"
          src="@/images/logo.png"
          class="d-inline-block align-top"
          alt="google Logo"
        />
      </div>
      <div class="col-sm-6">
        <span id="switchSpan">Switch to</span>
        <br />
        <b-button @click="switchList" id="switch" variant="outline-primary"
          >List</b-button
        >
        <b-button @click="switchCarousel" id="switch" variant="outline-primary"
          >Carousel</b-button
        >
      </div>
      <div class="col-sm">
        <label class="fileupload">
          <input id="file-selector" ref="file" type="file" multiple /><b-icon
            icon="camera"
          ></b-icon>
          Choose
        </label>
      </div>
      <div class="col-sm">
        <b-button
          id="addBtn"
          @click="handleFileUpload"
          variant="outline-primary"
          ><b-icon-arrow-up></b-icon-arrow-up> Send</b-button
        >
      </div>
    </div>
    <hr />
    <div class="d-flex flex-wrap" v-if="show">
      <div
        class="w-25 p-3"
        v-for="(url, index) in photosList"
        :key="index"
        deck
      >
        <b-card
          id="listPhoto"
          :img-src="
            `https://photo-album-hoza.s3.ap-northeast-2.amazonaws.com/` + url
          "
          img-alt="Image"
          img-top
        >
          <b-card-text id="cardText">
            {{ url }}
            <br />
            <form
              :action="
                `https://photo-album-hoza.s3.ap-northeast-2.amazonaws.com/` +
                  url
              "
              target="_blank"
            >
              <button class="btn btn-secondary">
                <b-icon icon="cloud-download" aria-hidden="true"></b-icon>
              </button>
              <b-button class="btn" @click="deletePhoto(url)"
                ><b-icon icon="trash-fill" aria-hidden="true"></b-icon
              ></b-button>
            </form>
          </b-card-text>
        </b-card>
      </div>
    </div>
    <div v-else id="carousel">
      <b-carousel
        id="carousel-1"
        v-model="slide"
        :interval="4000"
        controls
        indicators
        background="#ababab"
        img-width="500"
        img-height="480"
        style="text-shadow: 1px 1px 2px #333;"
        @sliding-start="onSlideStart"
        @sliding-end="onSlideEnd"
      >
        <b-carousel-slide v-for="(url, index) in photosList" :key="index">
          <template v-slot:img>
            <img
              id="carousel-image"
              class="d-block img-fluid w-100"
              :src="
                `https://photo-album-hoza.s3.ap-northeast-2.amazonaws.com/` +
                  url
              "
              alt="image slot"
            />
          </template>
          <form
            :action="
              `https://photo-album-hoza.s3.ap-northeast-2.amazonaws.com/` + url
            "
            target="_blank"
          >
            <button class="btn btn-secondary">
              <b-icon icon="cloud-download" aria-hidden="true"></b-icon>
            </button>
            <b-button @click="deletePhoto(url)"
              ><b-icon icon="trash-fill" aria-hidden="true"></b-icon
            ></b-button>
          </form>
        </b-carousel-slide>
      </b-carousel>
    </div>
  </div>
</template>

<script>
import AWS from "aws-sdk";
import "bootstrap/dist/css/bootstrap.css";
import "bootstrap-vue/dist/bootstrap-vue.css";

export default {
  data() {
    return {
      file: null,
      albumBucketName: "photo-album-hoza",
      bucketRegion: "ap-northeast-2",
      IdentityPoolId: "ap-northeast-2:c9123c64-387a-48b1-bf8f-78eccd3fd928",
      photosList: [],
      slide: 0,
      sliding: null,
      show: true,
    };
  },
  created() {
    this.getFiles();
  },
  methods: {
    switchCarousel() {
      return (this.show = false);
    },
    switchList() {
      return (this.show = true);
    },
    downloadPhoto(name) {
      return `https://photo-album-hoza.s3.ap-northeast-2.amazonaws.com/${name}`;
    },
    onSlideStart() {
      this.sliding = true;
    },
    onSlideEnd() {
      this.sliding = false;
    },
    handleFileUpload() {
      for (let i = 0; i < this.$refs.file.files.length; i++) {
        this.file = this.$refs.file.files[i];
        this.uploadPhoto();
      }
    },
    uploadPhoto() {
      AWS.config.update({
        region: this.bucketRegion,
        credentials: new AWS.CognitoIdentityCredentials({
          IdentityPoolId: this.IdentityPoolId,
        }),
      });

      var s3 = new AWS.S3({
        apiVersion: "2006-03-01",
        params: {
          Bucket: this.albumBucketName,
        },
      });

      let photoKey = this.file.name;

      s3.upload(
        {
          Key: photoKey,
          Body: this.file,
          ACL: "public-read",
        },
        (err, data) => {
          if (err) {
            console.log(err);
            return alert(
              "There was an error uploading your photo: ",
              err.message
            );
          }
          this.getFiles();
          console.log(data);
        }
      );
    },
    getFiles() {
      AWS.config.update({
        region: this.bucketRegion,
        credentials: new AWS.CognitoIdentityCredentials({
          IdentityPoolId: this.IdentityPoolId,
        }),
      });

      var s3 = new AWS.S3({
        apiVersion: "2006-03-01",
        params: {
          Bucket: this.albumBucketName,
        },
      });

      s3.listObjects(
        {
          Delimiter: "/",
        },
        (err, data) => {
          if (err) {
            return alert(
              "There was an error listing your albums: " + err.message
            );
          } else {
            this.photosList.length = 0;
            Object.values(data.Contents).forEach((e) => {
              this.photosList.push(e.Key);
            });
            console.log(data.Contents);
          }
        }
      );
    },
    deletePhoto(key) {
      AWS.config.update({
        region: this.bucketRegion,
        credentials: new AWS.CognitoIdentityCredentials({
          IdentityPoolId: this.IdentityPoolId,
        }),
      });

      var s3 = new AWS.S3({
        apiVersion: "2006-03-01",
        params: {
          Bucket: this.albumBucketName,
        },
      });

      s3.deleteObject(
        {
          Key: key,
        },
        (err, data) => {
          if (err) {
            return alert(
              "There was an error deleting your photo: ",
              err.message
            );
          }
          alert("Successfully deleted photo.");
          this.getFiles();
          console.log(data);
        }
      );
    },
  },
};
</script>

<style></style>
