<template>
  <div>
    <v-row justify="center">
      <v-col cols="11" sm="10" md="8">
        <v-card>
          <v-list>
            <v-card-title class="orange darken-1">
              <span class="text-h5 white--text">Просмотр изображений</span>
              <v-spacer></v-spacer>
            </v-card-title>
            <v-container>
              <div class="input-file-row">
                <label class="input-file">
                  <input type="file" id="catalog" ref="catalog" webkitdirectory directory multiple @change="open()"
                    class="input">
                  <span>Выберите дирректорию с изображениями</span>
                </label>

                <v-select v-model="select" :items="modes" label="Режим выравнивания/вписывания" dense
                  style="width: 60%;"></v-select>

                <!-- <v-img v-for="(item, i) in imagePreview.length" :key="i" :src="imagePreview[i]" v-show="showPreview[i]" /> -->
              </div>
              <v-alert dense text type="error" v-if="showText">Выберите дирректорию с изображениями и режим
                вырвнивания/вписывания</v-alert>
              <v-btn @click="openWindowShow()">Открыть окно просмотра</v-btn>
            </v-container>
          </v-list>
        </v-card>
      </v-col>
    </v-row>
  </div>
</template>

<style>
.input-file-row {
  width: 100%;
  height: 100%;
}

.input-file {
  position: relative;
  display: inline-block;
}

.input-file span {
  position: relative;
  display: inline-block;
  cursor: pointer;
  outline: none;
  text-decoration: none;
  font-size: 20px;
  vertical-align: middle;
  color: rgb(255 255 255);
  text-align: center;
  border-radius: 4px;
  background-color: rgb(35, 35, 35);
  line-height: 22px;
  height: 40px;
  padding: 10px 20px;
  box-sizing: border-box;
  border: none;
  margin: 0;
  transition: background-color 0.2s;
}

.input-file input[type=file] {
  position: absolute;
  z-index: -1;
  opacity: 0;
  display: block;
  width: 0;
  height: 0;
}

/* Focus */
.input-file input[type=file]:focus+span {
  box-shadow: 0 0 0 0.2rem rgba(0, 123, 255, .25);
}

/* Hover/Active */
.input-file:hover span {
  background-color: rgb(60, 60, 60);
}

.input-file:active span {
  background-color: rgb(35, 35, 35);
}

/* Disabled */
.input-file input[type=file]:disabled+span {
  background-color: #eee;
}

/* Список файлов */
.input-file-list {
  padding: 10px 0;
}

.input-file-list-item {
  margin-bottom: 10px;
}

.input-file-list-remove {
  color: red;
  text-decoration: none;
  display: inline-block;
  margin-left: 5px;
}
</style>

<script>
export default {
  name: 'index',
  data: () => ({
    modes: ['вписывание целиком', 'вписывание по вертикали', 'вписывание по горизонтали', 'просмотр в режиме 1:1'],
    catalog: [],
    showPreview: [],
    imagePreview: [],
    select: '',
    showText: false
  }),
  methods: {
    open() {
      console.log(this.$refs.catalog.files)
      this.catalog = this.$refs.catalog.files;
      console.log(this.catalog)
      this.imagePreview = []
      for (let i = 0; i < this.catalog.length; i++) {
        let reader = new FileReader();
        reader.addEventListener("load", function () {
          this.showPreview[i] = true;
          this.imagePreview.push(reader.result);
        }.bind(this), false);
        if (this.catalog[i]) {
          if (/\.(jpe?g|png|gif)$/i.test(this.catalog[i].name)) {
            reader.readAsDataURL(this.catalog[i]);
          }
        }
      }
    },
    openWindowShow() {
      this.showText = false
      if (this.imagePreview.length && this.select != '') {
        this.$router.push({
          name: "viewPage",
          params: {
            imgs: this.imagePreview,
            mode: this.select
          }
        })
      } else {
        this.showText = true
      }
    }
  }
}
</script>
