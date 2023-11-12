<template>
  <div id="app" class="body">
    <h1>Parcel Delivery Request App</h1>

    <div class="container">
      <h2>Request Creation Form</h2>

      <form @submit.prevent="createRequest" class="containerForm">
        <label for="fromCity"> From City: </label>
        <input
          id="fromCity"
          type="text"
          v-model="fromCity"
          placeholder="From City..."
          required
        />
        <label for="toCity">To city:</label>
        <input
          id="toCity"
          type="text"
          v-model="toCity"
          placeholder="To City..."
          required
        />
        <label for="parcelType">Select the type of parcel:</label>
        <select id="parcelType" v-model="parcelType" required>
          <option value="" disabled selected>Select...</option>
          <option value="gadgets">Gadgets</option>
          <option value="drinks">Drinks</option>
          <option value="clothes">Clothes</option>
          <option value="medicines">Medicines</option>
          <option value="other">Other</option>
        </select>
        <label for="dispatchDate">Choose date:</label>
        <input id="dispatchDate" type="date" v-model="dispatchDate" required />
        <label for="parcelDescription">Write a description</label>
        <textarea
          id="parcelDescription"
          v-model="parcelDescription"
          class="parcelDescription"
          placeholder="Parcel Description..."
        >
        </textarea>

        <button type="submit">Create Request</button>
      </form>

      <h2 v-if="requests.length > 0">List of Requests</h2>
      <Teleport to="body">
        <EditRequestModal
          v-show="isModalVisible"
          :editedRequest="editedRequest"
          @save-changes="saveEditedRequest"
          @cancel-changes="cancelEditedRequest"
        />
      </Teleport>

      <ul v-if="requests.length > 0" class="requestList">
        <li v-for="request in requests" :key="request.id" class="requestItem">
          <h3>{{ request.fromCity }} to {{ request.toCity }}</h3>
          <p>{{ request.parcelType }}</p>
          <p>{{ request.dispatchDate }}</p>
          <p>{{ request.parcelDescription }}</p>
          <div class="buttonContainer">
            <button @click="editRequest(request)" class="buttonEdit">
              Edit
            </button>
            <button @click="deleteRequest(request)" class="buttonDelete">
              Delete
            </button>
          </div>
        </li>
      </ul>
      <h2 v-else>No requests available.</h2>
    </div>
  </div>
</template>

<script lang="ts">
import { defineComponent } from 'vue';
import EditRequestModal from '../EditRequestModal/EditRequestModal.vue';

interface Request {
  id: number;
  fromCity: string;
  toCity: string;
  parcelType: string;
  dispatchDate: string;
  parcelDescription: string;
}

export default defineComponent({
  name: 'RequestForm',

  data() {
    return {
      fromCity: '',
      toCity: '',
      parcelType: '',
      dispatchDate: '',
      parcelDescription: '',
      requests: [] as Request[],
      isModalVisible: false,
      editedRequest: {} as Request,
    };
  },

  components: {
    EditRequestModal,
  },

  methods: {
    createRequest(): void {
      // Add the new request to the list of requests
      if (this.validateForm()) {
        this.requests.push({
          id: Date.now(),
          fromCity: this.fromCity,
          toCity: this.toCity,
          parcelType: this.parcelType,
          dispatchDate: this.dispatchDate,
          parcelDescription: this.parcelDescription,
        });
      }

      localStorage.setItem('requests', JSON.stringify(this.requests));

      // Clear the form fields
      this.fromCity = '';
      this.toCity = '';
      this.parcelType = '';
      this.dispatchDate = '';
      this.parcelDescription = '';
    },

    editRequest(request: Request): void {
      this.editedRequest = { ...request };
      this.isModalVisible = true;
    },

    saveEditedRequest(editedRequest: Request): void {
      // Update the request in the list
      const index = this.requests.findIndex(
        (request) => request.id === editedRequest.id,
      );
      if (index !== -1) {
        if (this.validateForm()) {
          this.requests.splice(index, 1, editedRequest);
          localStorage.setItem('requests', JSON.stringify(this.requests));
        }
      }

      // Hide the modal
      this.isModalVisible = false;
    },

    cancelEditedRequest(): void {
      // Hide the modal
      this.isModalVisible = false;
    },

    deleteRequest(request: Request): void {
      // Remove the request from the list of requests
      const index = this.requests.indexOf(request);

      if (index !== 1) {
        this.requests.splice(this.requests.indexOf(request), 1);

        localStorage.setItem('requests', JSON.stringify(this.requests));
      }
    },

    validateForm(): boolean {
      if (
        Number(this.dispatchDate.slice(0, 4)) < 1990 ||
        Number(this.dispatchDate.slice(0, 4)) > new Date().getFullYear() + 5
      ) {
        alert('Please enter a valid date');
        return false;
      }

      if (this.fromCity === this.toCity) {
        alert('The cities must be different');
        return false;
      }

      return true;
    },
  },

  mounted(): void {
    // Load the list of requests from LocalStorage
    const requestsFromLS = JSON.parse(
      localStorage.getItem('requests') || 'null',
    ) as Request[] | null;

    if (requestsFromLS) {
      this.requests = requestsFromLS;
    }
  },
});
</script>

<style scoped>
@import './RequestForm.css';
</style>
