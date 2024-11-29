<template>
  <div class="p-4">
    <h1 class="text-2xl font-bold mb-6">Role-Based Access Control (RBAC) UI</h1>

    <!-- User Management -->
    <div class="mb-8">
      <h2 class="text-xl font-bold mb-4">User Management</h2>
      <div class="flex gap-2 mb-4">
        <input
          v-model="newUser.name"
          type="text"
          placeholder="User Name"
          class="border p-2 rounded"
        />
        <select v-model="newUser.role" class="border p-2 rounded">
          <option value="" disabled selected>Select Role</option>
          <option v-for="role in roles" :key="role.id" :value="role.name">
            {{ role.name }}
          </option>
        </select>
        <button
          @click="addUser"
          class="bg-blue-500 text-white px-4 py-2 rounded"
        >
          Add User
        </button>
      </div>
      <p v-if="userError" class="text-red-500 mb-4">{{ userError }}</p>
      <table class="table-auto w-full border-collapse border border-gray-300">
        <thead>
          <tr>
            <th class="border px-4 py-2">Name</th>
            <th class="border px-4 py-2">Role</th>
            <th class="border px-4 py-2">Status</th>
            <th class="border px-4 py-2">Actions</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="user in users" :key="user.id">
            <td class="border px-4 py-2">{{ user.name }}</td>
            <td class="border px-4 py-2">{{ user.role }}</td>
            <td class="border px-4 py-2">{{ user.status }}</td>
            <td class="border px-4 py-2">
              <button
                @click="editUser(user)"
                class="bg-yellow-500 text-white px-4 py-2 rounded mr-2"
              >
                Edit
              </button>
              <button
                @click="deleteUser(user.id)"
                class="bg-red-500 text-white px-4 py-2 rounded"
              >
                Delete
              </button>
            </td>
          </tr>
        </tbody>
      </table>
    </div>

    <!-- Role Management -->
    <div class="mb-8">
      <h2 class="text-xl font-bold mb-4">Role Management</h2>
      <div class="flex gap-2 mb-4">
        <input
          v-model="newRole"
          type="text"
          placeholder="Role Name"
          class="border p-2 rounded"
        />
        <button
          @click="addRole"
          class="bg-green-500 text-white px-4 py-2 rounded"
        >
          Add Role
        </button>
      </div>
      <p v-if="roleError" class="text-red-500 mb-4">{{ roleError }}</p>
      <ul class="list-disc ml-5">
        <li v-for="role in roles" :key="role.id" class="mb-2">
          {{ role.name }}
          <button
            @click="deleteRole(role.id)"
            class="text-red-500 hover:underline ml-2"
            :disabled="isRoleInUse(role.name)"
            title="Cannot delete a role assigned to users"
          >
            Delete
          </button>
        </li>
      </ul>
    </div>

    <!-- Permissions Management -->
    <div>
      <h2 class="text-xl font-bold mb-4">Permission Management</h2>
      <p class="text-gray-500">
        Feature under construction! Add permissions for roles here.
      </p>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      users: [
        { id: 1, name: "Alice", role: "Admin", status: "Active" },
        { id: 2, name: "Bob", role: "Editor", status: "Inactive" },
      ],
      roles: [
        { id: 1, name: "Admin" },
        { id: 2, name: "Editor" },
      ],
      newUser: { name: "", role: "", status: "Active" },
      newRole: "",
      userError: "",
      roleError: "",
      editingUser: null, // To track the user being edited
    };
  },
  methods: {
    addUser() {
      if (!this.newUser.name || !this.newUser.role) {
        this.userError = "Please provide both user name and role.";
        return;
      }
      const roleExists = this.roles.some(
        (role) => role.name === this.newUser.role
      );
      if (!roleExists) {
        this.userError = "Selected role does not exist.";
        return;
      }
      if (this.editingUser) {
        this.editingUser.name = this.newUser.name;
        this.editingUser.role = this.newUser.role;
        this.editingUser.status = this.newUser.status;
        this.editingUser = null;
      } else {
        this.users.push({ ...this.newUser, id: Date.now() });
      }
      this.newUser = { name: "", role: "", status: "Active" };
      this.userError = "";
    },
    deleteUser(userId) {
      this.users = this.users.filter((user) => user.id !== userId);
    },
    addRole() {
      if (!this.newRole) {
        this.roleError = "Role name cannot be empty.";
        return;
      }
      if (this.roles.some((role) => role.name === this.newRole)) {
        this.roleError = "Role already exists.";
        return;
      }
      this.roles.push({ id: Date.now(), name: this.newRole });
      this.newRole = "";
      this.roleError = "";
    },
    deleteRole(roleId) {
      const roleToDelete = this.roles.find((role) => role.id === roleId);
      if (!this.isRoleInUse(roleToDelete.name)) {
        this.roles = this.roles.filter((role) => role.id !== roleId);
      }
    },
    isRoleInUse(roleName) {
      return this.users.some((user) => user.role === roleName);
    },
    editUser(user) {
      this.editingUser = user;
      this.newUser = { ...user }; // Populate the form with the user's data
    },
  },
};
</script>

<style>
body {
  font-family: Arial, sans-serif;
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}
</style>
