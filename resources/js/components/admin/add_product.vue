<template>
  <div>
    <section class="add-product">
      <form @submit.prevent="addNewProduct()">
         <section class="hero is-info welcome is-small">
          <div class="hero-body">
            <div class="container">
              <h1 class="title">Rodzaj produktu</h1>
            </div>
          </div>
        </section>
        <div class="control type-select">
          <label class="radio">
            <input type="radio" name="male" :checked="type == 'male'" @click="onTypeChanged('male')">
            Kamizelka męska
          </label>
          <label class="radio">
            <input type="radio" name="female" :checked="type == 'female'" @click="onTypeChanged('female')">
            Kamizelka damska
          </label>
          <label class="radio">
            <input type="radio" name="kid" :checked="type == 'kid'" @click="onTypeChanged('kid')">
            Kamizelka dziecięca
          </label>
          <label class="radio">
            <input type="radio" name="voucher" :checked="type == 'voucher'" @click="onTypeChanged('voucher')">
            Voucher
          </label>
        </div>
        <section class="hero is-info welcome is-small">
          <div class="hero-body">
            <div class="container">
              <h1 class="title">Podstawowe informacje</h1>
            </div>
          </div>
        </section>
        <div class="field">
          <div class="control">
            <input class="input" type="text" placeholder="Nazwa" v-model="productName" name="name" />
          </div>
        </div>
        <div class="field">
          <div class="control">
            <textarea
              class="textarea has-fixed-size"
              placeholder="Krótki opis produktu"
              rows="3"
              v-model="shortDescription"
              name="sentence"
            ></textarea>
            <p class="is-pulled-right char-counter">{{ shortDescription.length }}/{{ maxChars }}</p>
          </div>
          <div class="control">
            <textarea
              class="textarea has-fixed-size"
              placeholder="Short product description"
              rows="3"
              v-model="shortEnglishDescription"
              name="sentence"
            ></textarea>
            <p
              class="is-pulled-right char-counter"
            >{{ shortEnglishDescription.length }}/{{ maxChars }}</p>
          </div>
        </div>
        <br />
        <div class="field is-horizontal" v-if="type != 'voucher'">
          <div class="field-body">
            <div class="field">
              <div class="control is-expanded">
                <input class="input" type="text" placeholder="Cena" v-model="price" name="number" />
              </div>
            </div>
            <div class="field">
              <div class="control is-expanded">
                <input
                  class="input"
                  type="text"
                  placeholder="Cena promocyjna"
                  v-model="discountPrice"
                  name="number"
                />
              </div>
            </div>
          </div>
        </div>
        <div class="field">
          <div class="control">
            <textarea
              class="textarea has-fixed-size"
              placeholder="Długi opis produktu"
              rows="8"
              v-model="longDescription"
              name="paragraph"
            ></textarea>
          </div>
          <div class="control">
            <textarea
              class="textarea has-fixed-size english-long-description"
              placeholder="Long product description"
              rows="8"
              v-model="longEnglishDescription"
              name="paragraph"
            ></textarea>
          </div>
        </div>
        <div class="field">
          <div class="control">
            <textarea
              class="textarea has-fixed-size"
              placeholder="Opis materiałów"
              rows="3"
              v-model="materialsDescription"
              name="paragraph"
            ></textarea>
          </div>
          <div class="control">
            <textarea
              class="textarea has-fixed-size english-long-description"
              placeholder="Materials description"
              rows="3"
              v-model="englishMaterials"
              name="paragraph"
            ></textarea>
          </div>
        </div>

        <section class="hero is-info welcome is-small">
          <div class="hero-body">
            <div class="container">
              <h1 class="title">Rozmiary i stan magazynowy</h1>
              <h2 class="subtitle">Wybierz dostępne rozmiary oraz ich stan magazynowy</h2>
            </div>
          </div>
        </section>

        
        <div class="field" v-for="(size, index) in displayedSizes" :key="index">
          <div class="control">
            <label class="checkbox">
              <input
                type="checkbox"
                @click="onSizeChanged(size.sizeName, index)"
                v-model="sizesChecked[index]"
              />
              {{ size.sizeName }}
            </label>
          </div>
          <div class="control">
            <input
              class="input"
              type="text"
              placeholder="Stan magazynowy"
              v-model="quantities[index]"
            />
          </div>
        </div>        
        
        <section class="hero is-info welcome is-small">
          <div class="hero-body">
            <div class="container">
              <h1 class="title">Zdjęcia</h1>
            </div>
          </div>
        </section>

        <div class="file has-name is-boxed is-centered is-info">
          <label class="file-label">
            <input
              class="file-input"
              type="file"
              multiple
              name="resume"
              v-on:change="onImageChange"
            />
            <span class="file-cta">
              <span class="file-icon">
                <i class="fas fa-upload"></i>
              </span>
              <span class="file-label">Dodaj zdjęcia...</span>
            </span>
            <span class="file-name" v-if="images.length > 0">
              <span v-for="image in images" :key="image.id">{{ image.name + ' ' }}</span>
            </span>
          </label>
        </div>

        <div class="control">
          <button v-bind:class="['button is-primary', {'is-loading': isLoading}]">Dodaj</button>
        </div>
      </form>
    </section>
  </div>
</template>

<script>
export default {
  data() {
    return {
      productName: "",
      shortDescription: "",
      price: "",
      discountPrice: "",
      longDescription: "",
      materialsDescription: "",
      shortEnglishDescription: "",
      longEnglishDescription: "",
      englishMaterials: "",
      maxChars: 255,
      sizes: [],
      displayedSizes: [],
      images: [],
      imagesNames: [],
      sizesChecked: [],
      sizesNames: [],
      quantities: [],
      isLoading: false,

      type: "male",
    };
  },
  methods: {
    onImageChange(e) {
      this.images = e.target.files;
    },
    addNewProduct() {      
      for (let i = 0; i < this.images.length; i++) {
        this.imagesNames[i] = "/images/" + new Date().getTime() + i + ".jpg";
      }
      if (this.discountPrice == "" && this.type != 'voucher') {
        alert("Nalezy podac cenę promocyjną (0 w przypadku braku promocji)");
      } else {        
        this.isLoading = true;
        const config = {
          headers: { "content-type": "multipart/form-data" },
        };
        let formData = new FormData();
        formData.append("imagesNames", JSON.stringify(this.imagesNames));
        formData.append("name", this.productName);
        formData.append("shortDescription", this.shortDescription);
        formData.append("longDescription", this.longDescription);
        formData.append("materials", this.materialsDescription);
        formData.append(
          "shortEnglishDescription",
          this.shortEnglishDescription
        );
        formData.append("longEnglishDescription", this.longEnglishDescription);
        formData.append("englishMaterials", this.englishMaterials);
        formData.append("price", this.price == '' ? '0' : this.price);
        formData.append("discountPrice", this.type == 'voucher' ? '0' : this.discountPrice);
        formData.append("type", this.type);
        formData.append("sizes", JSON.stringify(this.sizesNames));
        if (!this.isVoucher) {          
          formData.append("quantities", JSON.stringify(this.quantities));
        }
                
        for (let i = 0; i < this.images.length; i++) {
          let image = this.images[i];
          formData.append("images[" + i + "]", image);
        }
        axios
          .post("/products", formData, config)
          .then((response) => {
            this.clearDataFields();
            this.isLoading = false;
            this.images = [];

            alert("Pomyślnie dodano nowy produkt");
          })
          .catch((error) => {
            this.isLoading = false;
            alert("Wystąpił błąd " + error);
          });
      }
    },
    onSizeChanged(name, index) {
      if (this.sizesNames[index]) {        
        this.sizesNames[index] = null;
      } else {
        this.sizesNames[index] = name;
      }
    },    
    onTypeChanged(selectedType) {
      this.type = selectedType;            

      switch (selectedType) {
        case 'male':
          this.displayedSizes = this.sizes.filter((size) => {
            return (parseInt(size.sizeName) >= 44 && parseInt(size.sizeName) <= 58);
          })
          break;

        case 'female':
          this.displayedSizes = this.sizes.filter((size) => {
            return (parseInt(size.sizeName) >= 34 && parseInt(size.sizeName) <= 44);
          })
          break;

        case 'kid':
          this.displayedSizes = this.sizes.filter((size) => {
            return (parseInt(size.sizeName) >= 104 && parseInt(size.sizeName) <= 146);
          })
          break;
        
        case 'voucher':          
          this.displayedSizes = this.sizes.filter((size) => {
            return isNaN(size.sizeName);
          })
          break;
      }
    },
    clearDataFields() {
      this.productName = "";
      this.shortDescription = "";
      this.shortEnglishDescription = "";
      this.price = "";
      this.discountPrice = "";
      this.longDescription = "";
      this.materialsDescription = "";
      this.longEnglishDescription = "";
      this.englishMaterials = "";
      this.sizesChecked = [];
      this.sizesNames = [];
      this.quantities = [];
      this.type = "male";
      this.voucherTypesChecked = [];
    },
  },
  created() {
    axios.get("admin/sizes/get").then((response) => {
      this.sizes = response.data;

      this.displayedSizes = this.sizes.filter(size => {     
        if(isNaN(size.sizeName)) return false;   
        return parseInt(size.sizeName) >= 44 && parseInt(size.sizeName) <= 58
      });
    });
  },
};
</script>

<style lang="scss">
.type-select{
  margin-bottom: 2rem;
  width: 100%;
  display: flex;
  justify-content: space-evenly;
}
.english-long-description {
  margin-top: 15px;
}
.add-product {
  margin-top: 20px;
  margin-left: 20px;
  margin-right: 20px;
}
.char-counter {
  font-size: 10pt;
}

.welcome {
  margin-bottom: 30px;
}

.checkboxes {
  margin-bottom: 30px;
}
</style>
