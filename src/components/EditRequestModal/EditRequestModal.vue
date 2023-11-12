<template>
  <div class="modalWrapper" @click="onClick">
    <div class="modal">
      <label for="editedFromCity">From City:</label>
      <input type="text" v-model="editedRequest.fromCity" id="editedFromCity" />
      <label for="editedToCity">To City:</label>
      <input type="text" v-model="editedRequest.toCity" id="editedToCity" />
      <label for="editedParcelType">Parcel Type:</label>
      <select v-model="editedRequest.parcelType" id="editedParcelType">
        <option value="gadgets">Gadgets</option>
        <option value="drinks">Drinks</option>
        <option value="clothes">Clothes</option>
        <option value="medicines">Medicines</option>
        <option value="other">Other</option>
      </select>
      <label for="editedDispatchDate">Dispatch Date:</label>
      <input
        type="date"
        v-model="editedRequest.dispatchDate"
        id="editedDispatchDate"
      />
      <label for="editedParcelDescription">Parcel Description:</label>
      <textarea
        v-model="editedRequest.parcelDescription"
        id="editedParcelDescription"
      ></textarea>
      <button @click="saveChanges" class="buttonSave">Save Changes</button>
      <button @click="cancelChanges" class="buttonCancel">Cancel</button>
    </div>
  </div>
</template>

<script>
export default {
  props: {
    show: Boolean,
    editedRequest: Object,
  },
  methods: {
    onClick(e) {
      if (e.target === e.currentTarget) {
        this.cancelChanges();
      }
    },
    saveChanges() {
      // Emit an event to inform the parent component about the changes
      this.$emit('save-changes', this.editedRequest);
    },
    cancelChanges() {
      this.$emit('cancel-changes', this.editedRequest);
    },
  },
};
</script>

<style scoped>
@import './EditRequestModal.css';
</style>
