<template>
    <div id="app">
      <h1>Welcome to Our App</h1>
  
      <!-- Conditional Rendering for Login and Registration -->
      <div v-if="!isLoggedIn">
        <div v-if="isRegistering">
          <h2>Register</h2>
          <form @submit.prevent="register">
            <input type="text" v-model="username" placeholder="Username" required />
            <input type="password" v-model="password" placeholder="Password" required />
            <button type="submit">Register</button>
            <button type="button" @click="toggleForm">Already have an account? Login</button>
          </form>
        </div>
  
        <div v-else>
          <h2>Login</h2>
          <form @submit.prevent="login">
            <input type="text" v-model="username" placeholder="Username" required />
            <input type="password" v-model="password" placeholder="Password" required />
            <button type="submit">Login</button>
            <button type="button" @click="toggleForm">Don't have an account? Register</button>
          </form>
        </div>
      </div>
  
      <!-- Welcome Screen -->
      <div v-else>
        <h2>Welcome, {{ loggedInUser .username }}!</h2>
        <button @click="logout">Logout</button>
  
        <div>
          <h3>Edit Account</h3>
          <form @submit.prevent="updateAccount">
            <input type="text" v-model="loggedInUser .username" placeholder="New Username" required />
            <input type="password" v-model="newPassword" placeholder="New Password" required />
            <button type="submit">Update Account</button>
          </form>
        </div>
  
        <div>
          <h3>Delete Account</h3>
          <button @click="deleteAccount">Delete My Account</button>
        </div>
      </div>
    </div>
  </template>
  
  <script>
  export default {
    data() {
      return {
        username: '',
        password: '',
        newPassword: '',
        isRegistering: false,
        isLoggedIn: false,
        registeredUsers: {}, // Store registered users
        loggedInUser:  null, // Store the currently logged-in user
      };
    },
    methods: {
      toggleForm() {
        this.isRegistering = !this.isRegistering;
        // Clear the inputs when toggling forms
        this.username = '';
        this.password = '';
        this.newPassword = '';
      },
      login() {
        // Check if the user is registered
        if (!this.registeredUsers[this.username]) {
          alert('Username does not exist. Please register first.');
          return;
        }
  
        // Check if the password is correct
        if (this.registeredUsers[this.username] !== this.password) {
          alert('Incorrect password. Please try again.');
          return;
        }
  
        // Simulate a login process
        this.isLoggedIn = true;
        this.loggedInUser  = { username: this.username, password: this.registeredUsers[this.username] };
      },
      register() {
        // Check if the username already exists
        if (this.registeredUsers[this.username]) {
          alert('Username already exists. Please choose a different username.');
          return;
        }
  
        // Simulate a registration process
        if (this.username && this.password) {
          // Store the registered user
          this.registeredUsers[this.username] = this.password; // Simple storage, not secure
          alert('Registration successful!');
          // Clear the input fields after registration
          this.username = '';
          this.password = '';
          this.isRegistering = false; // Switch to login form after registration
        }
      },
      updateAccount() {
        if (this.newPassword) {
          // Update the user's password
          this.registeredUsers[this.loggedInUser .username] = this.newPassword;
          alert('Account updated successfully!');
          this.newPassword = ''; // Clear the new password field
        }
      },
      deleteAccount() {
        if (confirm('Are you sure you want to delete your account? This action cannot be undone.')) {
          // Delete the user's account
          delete this.registeredUsers[this.loggedInUser .username];
          alert('Your account has been deleted.');
          this.logout(); // Log out the user after deletion
        }
      },
      logout() {
        this.isLoggedIn = false;
        this.username = '';
        this.password = '';
        this.newPassword = '';
        this.loggedInUser   = null; // Clear logged-in user data
      },
    },
  };
  </script>
  
  <style>
  #app {
    text-align: center;
    margin-top: 50px;
  }
  </style>