<template>
    <client-only>
        <div v-if="!loading">
            <v-row justify="center">
                <v-col cols="11" sm="10" md="8">
                    <v-card min-width="900px" min-height="900px" style="margin-bottom: 100px;">
                        <v-list>
                            <v-card-title class="orange darken-1">
                                <div class="input-file-row">
                                    <v-row>
                                        <v-col cols="5">
                                            <label class="input-file">
                                                <input type="file" id="catalog" ref="catalog" webkitdirectory directory
                                                    multiple @change="open()" class="input">
                                                <span>Изменить дирректорию</span>
                                            </label>
                                        </v-col>
                                        <v-col cols="5">
                                            <v-select v-model="select" :items="modes" label="Режим выравнивания/вписывания"
                                                dense style="width: 80%;"></v-select>
                                        </v-col>
                                        <v-col cols="2">
                                            <v-btn @click="goToHome()">Назад</v-btn>
                                        </v-col>
                                    </v-row>
                                </div>
                            </v-card-title>
                            <v-carousel hide-delimiters height="800px" style="width: 800px; margin: auto;">
                                <v-carousel-item v-for="(item, i) in imagesSrc" :key="i" style="width: 800px;">
                                    <v-img v-if="select == 'просмотр в режиме 1:1'" :src="item" cover min-height="800px"
                                        width="auto" max-height="800px"></v-img>
                                    <v-img v-if="select == 'вписывание целиком'" :src="item" contain height="100%"></v-img>
                                    <img v-if="select == 'вписывание по горизонтали'" :src="item" width="800px"
                                        height="auto" style="display: block; margin: auto 0;">
                                    <img v-if="select == 'вписывание по вертикали'" :src="item" height="800px" width="auto"
                                        style="display: block; margin: 0 auto;">
                                </v-carousel-item>
                            </v-carousel>
                        </v-list>
                    </v-card>
                </v-col>
            </v-row>
        </div>
    </client-only>
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
    name: 'viewPage',
    data: () => ({
        imagesSrc: [],
        idx: 0,
        loading: true,
        modes: ['вписывание целиком', 'вписывание по вертикали', 'вписывание по горизонтали', 'просмотр в режиме 1:1'],
        catalog: [],
        select: 'вписывание целиком',
    }),
    methods: {
        open() {
            console.log(this.$refs.catalog.files)
            this.catalog = this.$refs.catalog.files;
            console.log(this.catalog)
            this.imagesSrc = []
            for (let i = 0; i < this.catalog.length; i++) {
                let reader = new FileReader();
                reader.addEventListener("load", function () {
                    this.imagesSrc.push(reader.result);
                }.bind(this), false);
                if (this.catalog[i]) {
                    if (/\.(jpe?g|png|gif)$/i.test(this.catalog[i].name)) {
                        reader.readAsDataURL(this.catalog[i]);
                    }
                }
            }
            localStorage.setItem('imgs', JSON.stringify(this.imagesSrc))
        },
        goToHome() {
            this.$router.push({
                name: "index"
            })
        }
    },
    async beforeMount() {
        if (this.$route.params.imgs) {
            this.imagesSrc = this.$route.params.imgs
            this.select = this.$route.params.mode
            localStorage.setItem('imgs', JSON.stringify(this.$route.params.imgs))
            localStorage.setItem('mode', JSON.stringify(this.$route.params.mode))
        } else {
            this.imagesSrc = JSON.parse(localStorage.getItem('imgs'))
            this.select = JSON.parse(localStorage.getItem('mode'))
        }
        this.loading = false
    }
}
</script>