<script setup>
import { v4 as uuidv4 } from "uuid";
import { onMounted, reactive, ref } from "vue";

let formValues = reactive({ id: "", email: "", message: "" });
let messages = ref([]);
let error = ref(false);
let success = ref(false);
let errorMessage = ref("");
let successMessage = ref("");
let formError = ref(false);
let edited = reactive(null);
let editFormValues = reactive({ id: "", email: "", message: "" });

function validateForm() {
  let valid = true;
  if (formValues.email == "" || formValues.message == "") {
    valid = false;
  }

  return { valid };
}

async function submitForm() {
  if (!validateForm.valid) {
    formError.value = true;
  }

  formValues.id = uuidv4();
  messages.value.push(formValues);
  localStorage.setItem("messages", JSON.stringify(messages.value));

  alert("successfully created");

  setTimeout(() => {
    window.location.reload();
  }, 1000);
}

async function getMessages() {
  let msg = localStorage.getItem("messages");
  messages.value.push(...JSON.parse(msg));
}

async function startEdit(messages) {
  edited = messages;
  editFormValues.id = edited.id;
  editFormValues.email = edited.email;
  editFormValues.message = edited.message;
}

async function editMessages() {
  const index = messages.value.findIndex((msg) => msg.id === edited.id);
  messages.value[index].email = editFormValues.email;
  messages.value[index].message = editFormValues.message;
  localStorage.setItem("messages", JSON.stringify(messages.value));
}

function deleteMessage(id) {
  confirm("Do you want to delete message?");
  const index = messages.value.findIndex((msg) => msg.id === id);

  if (index !== -1) {
    messages.value.splice(index, 1);
    localStorage.setItem("messages", JSON.stringify(messages.value));
  }

  alert("Deleted successfully");
}

onMounted(() => {
  getMessages();
});
</script>



<template>
  <main>
    <div class="card w-100 mx-auto mt-5 mb-1">
      <h1>A simple Crud App with vue 3</h1>
      <span class="alert alert-success" role="alert" v-if="success">
        {{ successMessage }} .</span
      >

      <span class="alert alert-danger" role="alert" v-if="error">
        {{ errorMessage }} .</span
      >
      <h2 className="text-center mt-2">Testing</h2>
      <form class="mt-4 text-center">
        <div class="mb-3 mx-auto w-75">
          <label class="form-label">Email</label>
          <br />
          <input
            type="email"
            name="email"
            class="form-control"
            placeholder="name@example.com"
            v-model="formValues.email"
          />
          <small class="text-danger" v-if="formError && formValues.email == ''">
            Email is required
          </small>
          <label class="form-label mt-2">Message</label>
          <textarea
            class="form-control mx-auto w-75"
            rows="3"
            v-model="formValues.message"
          ></textarea>
          <small
            class="text-danger"
            v-if="formError && formValues.message == ''"
          >
            Message is required
          </small>
        </div>
        <button
          @click.prevent="submitForm"
          type="submit"
          class="btn btn-primary mb-2"
        >
          Submit
        </button>
      </form>

      <h1>Get All message</h1>
      <div v-if="messages.length != 0">
        <ul
          class="list-group"
          v-for="(msg, index) in messages"
          :key="index"
          :value="msg.id"
        >
          <li class="list-group-item">
            {{ index + 1 }} {{ msg.email + " " + msg.message }}

            <button
              class="btn btn-primary"
              data-bs-toggle="modal"
              data-bs-target="#editModal"
              @click="startEdit(msg)"
            >
              Edit
            </button>
            <button class="btn btn-danger" @click="deleteMessage(msg.id)">
              Delete
            </button>
          </li>
        </ul>
      </div>
    </div>

    <!------------------------------ EDIT MODAL -------------------------------->

    <!-- Modal -->
    <div
      class="modal fade"
      id="editModal"
      tabindex="-1"
      aria-labelledby="editModalLabel"
      aria-hidden="true"
    >
      <div class="modal-dialog">
        <div class="modal-content">
          <div class="modal-header">
            <h1 class="modal-title fs-5" id="editModalLabel">Edit</h1>
            <button
              type="button"
              class="btn-close"
              data-bs-dismiss="modal"
              aria-label="Close"
            ></button>
          </div>
          <div class="modal-body">
            <form class="mt-4 text-center">
              <div class="mb-3 mx-auto w-75">
                <label class="form-label">Email</label>
                <br />
                <input
                  type="email"
                  name="email"
                  class="form-control"
                  placeholder="name@example.com"
                  v-model="editFormValues.email"
                />

                <label class="form-label mt-2">Message</label>
                <textarea
                  class="form-control mx-auto w-75"
                  rows="3"
                  v-model="editFormValues.message"
                ></textarea>
              </div>
            </form>
          </div>
          <div class="modal-footer">
            <button
              type="button"
              class="btn btn-secondary"
              data-bs-dismiss="modal"
            >
              Close
            </button>
            <button
              @click.prevent="editMessages"
              type="submit"
              class="btn btn-primary mb-2"
            >
              Submit
            </button>
          </div>
        </div>
      </div>
    </div>
  </main>
</template>



