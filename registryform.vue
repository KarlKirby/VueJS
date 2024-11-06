<template>
  <div id="app">
    <h1>Welcome to Our App</h1>

    <div class="form-container">
      <div v-if="!isLoggedIn">
        <div v-if="isRegistering" class="form-section">
          <h2>Register</h2>
          <form @submit.prevent="register" class="form">
            <input
              type="text"
              v-model="username"
              placeholder="Username"
              required
            />
            <input
              type="password"
              v-model="password"
              placeholder="Password"
              required
            />
            <button type="submit" class="btn">Register</button>
            <button type="button" @click="toggleForm" class="link-btn">
              Already have an account? Login
            </button>
          </form>
        </div>

        <div v-else class="form-section">
          <h2>Login</h2>
          <form @submit.prevent="login" class="form">
            <input
              type="text"
              v-model="username"
              placeholder="Username"
              required
            />
            <input
              type="password"
              v-model="password"
              placeholder="Password"
              required
            />
            <button type="submit" class="btn">Login</button>
            <button type="button" @click="toggleForm" class="link-btn">
              Don't have an account? Register
            </button>
          </form>
        </div>
      </div>

      <div v-else class="welcome-section">
        <h2>Welcome, {{ loggedInUser.username }}!</h2>
        <button @click="logout" class="btn">Logout</button>
        <div class="account-section">
          <h3>Update Username</h3>
          <button
            @click="showUsernameUpdateForm = !showUsernameUpdateForm"
            class="btn"
          >
            {{ showUsernameUpdateForm ? "Cancel" : "Update Username" }}
          </button>

          <div v-if="showUsernameUpdateForm" class="username-update-form">
            <form @submit.prevent="updateUsername" class="form">
              <div>
                <label for="currentPassword">Current Password:</label>
                <input
                  type="password"
                  id="currentPassword"
                  v-model="currentPassword"
                  placeholder="Current Password"
                  required
                />
              </div>
              <div>
                <label for="newUsername">New Username:</label>
                <input
                  type="text"
                  id="newUsername"
                  v-model="newUsername"
                  placeholder="New Username"
                  required
                />
              </div>
              <button type="submit" class="btn">Update Username</button>
            </form>
          </div>

          <h3>Change Password</h3>
          <button
            @click="showPasswordChangeForm = !showPasswordChangeForm"
            class="btn"
          >
            {{ showPasswordChangeForm ? "Cancel" : "Change Password" }}
          </button>

          <div v-if="showPasswordChangeForm" class="password-change-form">
            <form @submit.prevent="changePassword" class="form">
              <div>
                <label for="oldPassword">Old Password:</label>
                <input
                  type="password"
                  id="oldPassword"
                  v-model="oldPassword"
                  placeholder="Old Password"
                  required
                />
              </div>
              <div>
                <label for="newPassword">New Password:</label>
                <input
                  type="password"
                  id="newPassword"
                  v-model="newPassword"
                  placeholder="New Password"
                  required
                />
              </div>
              <button type="submit" class="btn">Change Password</button>
            </form>
          </div>
        </div>

        <div class="delete-section">
          <h3>Delete Account</h3>
          <button @click="deleteAccount" class="btn delete-btn">
            Delete My Account
          </button>
        </div>
      </div>
    </div>

    <!-- Modal for displaying messages -->
    <Modal
      :isVisible="modalVisible"
      :title="modalTitle"
      :message="modalMessage"
      @close="closeModal"
    />
  </div>
</template>

<script>
import Modal from "./Modal.vue";

export default {
  components: {
    Modal,
  },
  data() {
    return {
      username: "",
      password: "", // This will be used for both current password and old password
      currentPassword: "", // Used for verifying current password
      oldPassword: "",
      newUsername: "", // New username to be set
      newPassword: "", // New password to be set
      isRegistering: false,
      isLoggedIn: false,
      registeredUsers: {},
      loggedInUser: null,
      modalVisible: false,
      modalTitle: "",
      modalMessage: "",
      showUsernameUpdateForm: false, // Toggle for username update form
      showPasswordChangeForm: false, // Toggle for password change form
    };
  },
  methods: {
    toggleForm() {
      this.isRegistering = !this.isRegistering;
      this.username = "";
      this.password = "";
      this.newUsername = "";
      this.newPassword = "";
    },
    login() {
      if (!this.registeredUsers[this.username]) {
        this.showModal(
          "Error",
          "Username does not exist. Please register first."
        );
        return;
      }

      if (this.registeredUsers[this.username] !== this.password) {
        this.showModal("Error", "Incorrect password. Please try again.");
        return;
      }

      this.isLoggedIn = true;
      this.loggedInUser = {
        username: this.username,
        password: this.registeredUsers[this.username],
      };
    },
    register() {
      if (this.registeredUsers[this.username]) {
        this.showModal(
          "Error",
          "Username already exists. Please choose a different one."
        );
        return;
      }

      this.registeredUsers[this.username] = this.password;
      this.showModal("Success", "Registration successful! You can now log in.");
      this.toggleForm();
    },
    changePassword() {
      if (this.loggedInUser.password !== this.password) {
        this.showModal("Error", "Old password is incorrect. Please try again.");
        return;
      }

      this.registeredUsers[this.loggedInUser.username] = this.newPassword;
      this.showModal("Success", "Password changed successfully!");
      this.password = "";
      this.newPassword = "";
      this.showPasswordChangeForm = false; // Hide the form after update
    },
    updateUsername() {
      if (this.loggedInUser.password !== this.password) {
        this.showModal(
          "Error",
          "Current password is incorrect. Please try again."
        );
        return;
      }

      if (this.registeredUsers[this.newUsername]) {
        this.showModal(
          "Error",
          "Username already exists. Please choose a different one."
        );
        return;
      }

      // Update username in registeredUsers
      this.registeredUsers[this.newUsername] =
        this.registeredUsers[this.loggedInUser.username];
      delete this.registeredUsers[this.loggedInUser.username];
      this.loggedInUser.username = this.newUsername;
      this.showModal("Success", "Username updated successfully!");
      this.password = "";
      this.newUsername = "";
      this.showUsernameUpdateForm = false; // Hide the form after update
    },
    showModal(title, message) {
      this.modalTitle = title;
      this.modalMessage = message;
      this.modalVisible = true;
    },
    closeModal() {
      this.modalVisible = false;
    },
    logout() {
      this.isLoggedIn = false;
      this.loggedInUser = null;
      this.username = "";
      this.password = "";
      this.newUsername = "";
      this.newPassword = "";
    },
    deleteAccount() {
      delete this.registeredUsers[this.loggedInUser.username];
      this.showModal("Success", "Account deleted successfully.");
      this.logout();
    },
  },
};
</script>

<style scoped>
#app {
  text-align: center;
  margin-top: 50px;
}

.form-container {
  max-width: 400px;
  margin: auto;
  padding: 20px;
  border: 1px solid #ccc;
  border-radius: 8px;
  box-shadow: 0 2px 10px rgba(0, 0, 0, 0.1);
}

.form-section {
  margin-bottom: 20px;
}

.form {
  display: flex;
  flex-direction: column;
}

input {
  margin: 10px 0;
  padding: 10px;
  border: 1px solid #ccc;
  border-radius: 4px;
}

.btn {
  padding: 10px;
  background-color: #28a745;
  color: white;
  border: none;
  border-radius: 4px;
  cursor: pointer;
}

.btn:hover {
  background-color: #218838;
}

.link-btn {
  background: none;
  color: #007bff;
  border: none;
  cursor: pointer;
  text-decoration: underline;
}

.link-btn:hover {
  text-decoration: none;
}

.welcome-section {
  margin-top: 20px;
}

.delete-btn {
  background-color: #dc3545;
}

.delete-btn:hover {
  background-color: #c82333;
}
</style>
