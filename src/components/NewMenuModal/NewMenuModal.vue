<script>
export default {
  name: 'NewFoodModal',
  props: {
    category: String,
    toggleModal: Function,
    editInfo: Object,
    reset: Function,
  },
  data: function() {
    return {
      foodItem: {
        header: this.editInfo ? 'EDIT' : 'NEW',
        name: this.editInfo ? this.editInfo.name : '',
        description: this.editInfo ? this.editInfo.description : '',
        price: this.editInfo ? this.editInfo.price : '',
        submitButton: this.editInfo ? 'update' : 'add',
      },
      showModal: false,
      priceInputFocused: false,
      fileName: '',
    };
  },
  mounted() {
    this.showModal = true;
  },
  beforeDestroy() {
    this.reset();
  },
  methods: {
    async handleSubmit() {
      const data = {
        name: this.foodItem.name,
        description: this.foodItem.description,
        price: Number(this.foodItem.price),
      };
      if (this.editInfo) {
        data.id = this.item.id;
        const editedProduct = await this.$store.dispatch('apolloQuery', {
          queryName: 'UPDATE_PRODUCT',
          queryType: 'mutation',
          data: data,
        });
      } else {
        const addedProduct = await this.$store.dispatch('apolloQuery', {
          queryName: 'ADD_PRODUCT',
          queryType: 'mutation',
          data: data,
        });

        await this.$store.dispatch('apolloQuery', {
          queryName: 'ADD_CATEGORY_TO_PRODUCT',
          queryType: 'mutation',
          data: {
            id: addedProduct.createProduct.id,
            category: this.category.toLowerCase(),
          },
        });
      }

      this.toggleModal();
    },
    filesAdded(e) {
      const fileName = e.target.value.split('\\');
      this.fileName = fileName[fileName.length - 1];
    },
  },
};
</script>

<template>
  <div class="NewEventModal">
    <div id="modal" :class="{'modal-visible': showModal}">
      <div class="modal-container">
        <i @click="toggleModal" class="material-icons close-modal">close</i>
        <h2>{{foodItem.header}} {{category.toUpperCase()}} ITEM</h2>
        <hr>
        <form @submit.prevent="handleSubmit" class="modal-form">

          <div class="modal-inputs-container">
            <div class="form-inline">
              <div class="form-field">
                <p class="addnew-label">Name:</p>
                <input v-model="foodItem.name" type="text" />
              </div>

              <div class="form-field">
                <p class="addnew-label-price">Price:</p>
                <label :class="{active: priceInputFocused}" for="txt">€
                  <input @focus="priceInputFocused = true" @blur="priceInputFocused = false" v-model="foodItem.price" type="text" id="txt" />
                </label>
              </div>
            </div>

            <div class="form-field">
              <p class="addnew-label">Description:</p>
              <textarea v-model="foodItem.description"></textarea>
            </div>

            <div class="form-field">
              <p class="addnew-label">Photo:</p>
              <div class="file-input">
                  <label for="file-upload" class="custom-file-upload">
                     Upload pictures
                </label>
                <input @change="filesAdded" type="file" id="file-upload" accept="image/*"/>
                  <p class="file-input-text">{{fileName}}</p>
                </div>
                <hr>
            </div>
          </div>

          <div class="form-buttons-wrapper">
            <p id="addnew-error"></p>
            <input type="submit" class="btn addnew-submit" value="ADD ITEM" />
          </div>
        </form>
      </div>
    </div>
    <div @click="toggleModal(); reset()" :class="{'modal-visible': showModal}" id="mask"></div>
  </div>
</template>

<style src="./NewMenuModal.css" scoped>

