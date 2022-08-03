<template>
  <div>
    <div class="container">
      <div class="row mt-5 mb-5">
        <div class="col-md-12 d-flex justify-content-center align-items-center">
          <h1>Yasin Demirkaya Dynamic Draggable Sortable Dashboard</h1>
        </div>
      </div>
      <!-- Layout'a ilk ekleme yapıldığında burayı görünür yapıyorum -->
      <!-- Layout arrayindeki item kadar grid-item yaratıyorum -->

      <!-- Sanırım satırları bir bütün halinde tutup birbirleri arasında sorting yapmak dışında taskta belirtilen her şeyi yaptım. -->
      <!-- Aslında amacım her satır seçiminde yeni <grid-layout>'lar oluşturmaktı. Böylece içlerinde kendi <grid-item>'ları olan unique satırlar elde etmiş olacaktım -->
      <!-- Kullandığım kütüphanede grid-layout'lar arasında drag & drop ya da sorting gibi bir feature bulunmadığı için bu grid-layout'ları da farklı bir draggable kütüphanesi ile birbirleri arasında sortlayacaktım -->
      <!-- Böylece taskın tamamını yapmış olacaktım ancak çok yoğun bir hafta geçirdim ve bu kadarını yapmaya ancak fırsat bulabildim -->
      <!-- Task oldukça hoşuma gitti çünkü çalıştığım iki firmada da bu tarz dashboardlar tasarlamıştım ve bu paketlerle sıklıkla uğraşmıştım -->
      <!-- Eğer benimle teknik mülakatta görüşmek isterseniz hem daha önce bu tarz işler yaptığım projeleri size gösterip bahsedebilirim. -->
      <!-- Ayrıca bu taskı hazırlayan kişiden aslında nasıl yapıldığını dinlemeyi çok isterim. Şimdiden ilginiz için teşekkür ederim. -->
      <grid-layout v-if="layout != null" :layout.sync="layout" :col-num="colNum" :row-height="30" :is-draggable="true" :is-resizable="false" :is-mirrored="false" :vertical-compact="true" :margin="[10, 10]" :use-css-transforms="true">
        <grid-item v-for="item in layout" :x="item.x" :y="item.y" :w="item.w" :h="item.h" :i="item.i" :key="item.i" drag-allow-from=".vue-draggable-handle" drag-ignore-from=".no-drag">
          <div class="col-md-12 d-flex flex-column justify-content-center align-items-center custom-border p-3">
            <font-awesome-icon icon="fa-solid fa-plus" size="xl" />
          </div>
          <div>
            <span class="remove" @click="removeItem(item.i)">x</span>
            <span class="vue-draggable-handle"></span>
          </div>
        </grid-item>
      </grid-layout>

      <!-- Yeni satır ekle... -->
      <div class="row mt-5 custom-border p-5" v-if="!isDashboardMenuVisible">
        <div class="col-md-12 d-flex flex-column justify-content-center align-items-center">
          <button class="btn btn-outline-dark rounded-pill" @click="isDashboardMenuVisible = true">
            <font-awesome-icon icon="fa-solid fa-plus" size="xl" />
          </button>
          Yeni satır ekle
        </div>
      </div>
      <!-- Satır yapısını seçiniz... -->
      <div class="row mt-5 custom-border p-5" v-else>
        <div class="col-md-12 d-flex flex-column justify-content-center align-items-center">
          <div>
            <label>Satır yapısını seçiniz</label>
          </div>
          <!-- Satır seçenekleri -->
          <div class="row">
            <div class="col-md-2 p-3 d-flex justify-content-center align-items-center" v-for="item in sectiondataFirst" :key="item.id">
              <button class="btn" @click="getColumnData(item.kolon, item.kolon.length); addItem(item.kolon); isDashboardMenuVisible = false">
                <svg xmlns:xlink="http://www.w3.org/1999/xlink" x="0" y="0" width="100" height="50" style="fill: #cecece;">
                  <path :d="item.svgattr" />
                </svg>
              </button>
            </div>
          </div>
          <div class="row">
            <div class="col-md-2 p-3 d-flex justify-content-center align-items-center" v-for="item in sectiondataSecond" :key="item.id">
              <button class="btn" @click="getColumnData(item.kolon, item.kolon.length); addItem(item.kolon); isDashboardMenuVisible = false">
                <svg xmlns:xlink="http://www.w3.org/1999/xlink" x="0" y="0" width="100" height="50" style="fill: #cecece;">
                  <path :d="item.svgattr" />
                </svg>
              </button>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
  import VueGridLayout from 'vue-grid-layout';
  import sectiondata from "@/assets/json/sectiondata.json";

  export default {
    name: 'MainComponent',
    data() {
      return {
        columns: [],
        numberOfColumns: null,
        sectiondata: sectiondata,
        isDashboardMenuVisible: false,
        layout: [],
        index: 0,
        colNum: 100
      }
    },
    computed: {
      sectiondataFirst() {
        return this.sectiondata.slice(0, 6)
      },
      sectiondataSecond() {
        return this.sectiondata.slice(6, 12)
      }
    },
    components: {
      GridLayout: VueGridLayout.GridLayout,
      GridItem: VueGridLayout.GridItem
    },
    methods: {
      getColumnData(kolon, length) {
        this.columns = kolon;
        // Seçilen satır yapısındaki kolonların listesi
        console.log("Kolonlar Listesi: ", this.columns)
        for (var i = 0; i < kolon.length; i++) {
          console.log("Kolon ", i + 1, ":", kolon[i])
        }
        this.numberOfColumns = length;
        console.log("Kolon Array'inin Eleman Sayısı: ", length)
      },
      addItem(kolon) {
        // Satırın ilk elemanının x'i 0 olacağı için bu şekilde başlatıyorum
        var x = 0;
        for (var i = 0; i < kolon.length; i++) {
          this.layout.push({
            x: x,
            y: 0,
            w: kolon[i].width,
            h: 2,
            i: this.index,
          });
          x += kolon[i].width;

          // Layout arrayinin objelerinin i elemanları unique olmalı bu yüzden her seferinde artırıyorum
          this.index++;

          // Döngü kolon'un eleman sayısı kadar döneceğinden ilk elemana 0 girildikten sonra ikinci elemanın da ilk elemanın üzerine binmemesi için
          // x'i o kolonun widthi kadar artırıyorum böylece hemen yanına konumlanıyor
        }
      },
      removeItem(val) {
        const index = this.layout.map(item => item.i).indexOf(val);
        this.layout.splice(index, 1);

        // Yeniden eklemeye başladığımızda index'in güncel kalması için her remove işleminde indexi azaltıyorum
        this.index--;
      },
    },
  }
</script>

<style>
  .custom-border {
    border: 1px solid #7c7c7c;
    border-style: dashed;
  }
  .vue-draggable-handle {
    border: 1px solid #747474;
    position: absolute;
    width: 10px;
    height: 10px;
    top: 0;
    right: 0;
    padding: 0 8px 8px 0;
    background-origin: content-box;
    background-color: black;
    box-sizing: border-box;
    border-radius: 10px;
    cursor: pointer;
  }
  .vue-grid-item .no-drag {
    height: 100%;
    width: 100%;
  }
  .columns {
    -moz-columns: 120px;
    -webkit-columns: 120px;
    columns: 120px;
  }
  .remove {
    position: absolute;
    right: 2px;
    top: 0;
    cursor: pointer;
  }
  .vue-grid-item .resizing {
    opacity: 0.9;
  }
  .vue-grid-item .text {
    font-size: 24px;
    text-align: center;
    position: absolute;
    top: 0;
    bottom: 0;
    left: 0;
    right: 0;
    margin: auto;
    height: 100%;
    width: 100%;
  }
  .vue-grid-item .no-drag {
    height: 100%;
    width: 100%;
  }
  .vue-grid-item .minMax {
    font-size: 12px;
  }
  .vue-grid-item .add {
    cursor: pointer;
  }
  .vue-draggable-handle {
    position: absolute;
    width: 20px;
    height: 20px;
    top: 0;
    left: 0;
    background: url("data:image/svg+xml;utf8,<svg xmlns='http://www.w3.org/2000/svg' width='10' height='10'><circle cx='5' cy='5' r='5' fill='#999999'/></svg>")
      no-repeat;
    background-position: bottom right;
    padding: 0 8px 8px 0;
    background-repeat: no-repeat;
    background-origin: content-box;
    box-sizing: border-box;
    cursor: pointer;
  }
  .remove:hover {
    cursor: pointer;
  }
</style>