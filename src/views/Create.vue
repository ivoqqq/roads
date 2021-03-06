<template>
  <div class="create-container container">
    <form class="input-form" @submit.prevent="addData">
      <div class="error" v-if="$v.place.title.$error">
        <p v-if="!$v.place.title.minLength">At least 3 characters</p>
        <p v-if="!$v.place.title.maxLength">Maximum 30 characters</p>
        <p v-if="!$v.place.title.required">No empty fields</p>
      </div>
      <textarea
        class="title"
        type="text"
        v-model="place.title"
        placeholder="type in place to visit..."
        @blur="$v.place.title.$touch()"
      />
      <div class="error" v-if="$v.place.description.$error">
        <p v-if="!$v.place.description.minLength">At least 6 characters</p>
        <p v-if="!$v.place.description.maxLength">Maximum 300 characters</p>
        <p v-if="!$v.place.description.required">No empty fields</p>
      </div>
      <textarea
        class="description"
        type="text"
        placeholder="about the place..."
        v-model="place.description"
        @blur="$v.place.description.$touch()"
      />

      <div class="error" v-if="$v.finishedUploadTask.$error">
        <p v-if="!$v.finishedUploadTask.required">You didn't choose any file</p>
      </div>
      <div class="image-container">
        <label for="input-image" class="input-file-label">{{ file }}</label>

        <input
          id="input-image"
          ref="upload"
          @change="uploadFile"
          accept="image/*"
          type="file"
          :disabled="disableFileInput"
          @blur="$v.finishedUploadTask.$touch()"
        />
      </div>
      <progress
        v-if="progress != null"
        class="progress-bar"
        :value="progress"
        max="100"
      ></progress>
      <button :disabled="$v.$invalid">Create</button>
    </form>
  </div>
</template>

<script>
import { destinationService } from "@/services/destinationService";
import { authService } from "@/services/authService";
import {
  required,
  minLength,
  maxLength,
  minValue,
} from "vuelidate/lib/validators";

export default {
  mixins: [destinationService, authService],
  validations: {
    place: {
      title: {
        required,
        minLength: minLength(3),
        maxLength: maxLength(30),
      },
      description: {
        required,
        minLength: minLength(6),
        maxLength: maxLength(300),
      },
    },
    finishedUploadTask: {
      required,
      minValue: minValue(1),
    },
  },
  data() {
    return {
      place: {
        userID: this.currentUserID(),
        username: this.currentUsername(),
        title: "",
        description: "",
        photo: "",
        created: new Date(),
      },
      imageData: null,
      progress: null,
      file: "ADD PICTURE",
      finishedUploadTask: null,
      disableFileInput: false,
    };
  },
  methods: {
    addData() {
      this.createData();
    },
    uploadFile(event) {
      this.addFile(event);
    },
  },
  watch: {
    progress() {
      if (this.progress !== null) {
        this.disableFileInput = true;
      }
    },
  },
};
</script>

<style lang="scss" scoped>
@import "@/styles/container";
@import "@/styles/progress-bar";
@import "@/styles/inputs";

.create-container {
  display: flex;
  align-items: center;
  justify-content: center;
}
.create-container::before {
  background-image: url("../assets/bench-with-the-view-in-austrian-mountains-2210x1658.jpg");
  filter: grayscale(50%) brightness(60%);
  z-index: -1;
}
.input-form {
  text-align: center;
  position: relative;
  width: 40%;
  padding: 80px 40px 40px 40px;

  &:before {
    content: "";
    width: 100%;
    height: 100%;
    background-color: rgba(20, 23, 27, 0.9);
    position: absolute;
    top: 0;
    left: 0;
    border-radius: 4px;
    z-index: -1;
  }
  .title {
    height: 40px;
  }
}

input[type="file"] {
  display: none;
}
.input-file-label {
  display: inline-block;
  line-height: 30px;
  position: relative;
  width: 100px;
  background-color: olive;
  color: white;
  cursor: pointer;
  opacity: 0.8;
  font-size: 14px;
  transition: all 0.2s ease-in-out;
  margin: 5px 0 22px 0;
  padding: 0 5px;
  border-radius: 2px;

  &:hover {
    opacity: 1;
  }
}

button {
  width: 100%;
  height: 40px;
  background-color: olive;
  color: white;
  border: none;
  cursor: pointer;
  opacity: 0.8;
  font-size: 22px;
  transition: all 0.1s ease-in-out;
  border-radius: 4px;
  position: relative;
  bottom: 0;
  margin: 20px 0 auto;

  &:hover {
    opacity: 1;
  }

  &:disabled {
    background-color: rgb(204, 204, 204);
    color: rgb(102, 102, 102);
    opacity: 0.8;
    transform: scale(1);
  }
}
</style>
