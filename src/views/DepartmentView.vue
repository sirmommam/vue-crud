<template>
    <div class="container">
      <h1>Department Management</h1>
  
      <!-- ปุ่มเพิ่มข้อมูล -->
      <button @click="showAddPopup = true" class="btn btn-primary">Add Department</button>
  
      <!-- Popup สำหรับเพิ่มข้อมูล -->
      <div v-if="showAddPopup" class="popup-overlay">
        <div class="popup">
          <h2>Add Department</h2>
          <form @submit.prevent="addDepartment">
            <input type="text" v-model="newDepartment.dept_id" placeholder="Department ID" required /><br>
            <input type="text" v-model="newDepartment.dept_name" placeholder="Department Name" required /><br>
            
            <button type="submit">Add</button>
            <button type="button" @click="showAddPopup = false">Cancel</button>
          </form>
        </div>
      </div>
  
      <!-- ตารางแสดงข้อมูล -->
      <table border="1" class="table table-borerded">
        <thead>
          <tr>
            <th>Department ID</th>
            <th>Department Name</th>
            <th>Actions</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="Department in Departments" :key="Department.dept_id">
            <td>{{ Department.dept_id }}</td>
            <td>{{ Department.dept_name }}</td>
            <td>
              <button @click="openEditPopup(Department)" class="btn btn-warning btn-sm ">Edit</button>
              <button @click="confirmDelete(Department.dept_id)" class="btn btn-danger btn-sm ms-2">Delete</button>
            </td>
          </tr>
        </tbody>
      </table>
  
      <!-- Popup สำหรับแก้ไขข้อมูล -->
      <div v-if="showEditPopup" class="popup-overlay">
        <div class="popup">
          <h2>Edit Department</h2>
          <form @submit.prevent="updateDepartment">
            <input type="text" v-model="editedDepartment.dept_name" placeholder="Department Name" required /><br>
            <button type="submit">Update</button>
            <button type="button" @click="showEditPopup = false">Cancel</button>
          </form>
        </div>
      </div>
  
      <!-- Popup ยืนยันการลบ -->
      <div v-if="showDeletePopup" class="popup-overlay">
        <div class="popup">
          <h2>Confirm Delete</h2>
          <p>Are you sure you want to delete this Department?</p>
          <button @click="deleteDepartment(deletedept_id)">Yes</button>
          <button @click="showDeletePopup = false">Cancel</button>
        </div>
      </div>
    </div>
  </template>
  
  <script>
  import axios from "axios";
  
  export default {
    data() {
      return {
        Departments: [],
        newDepartment: {
          dept_name: "",
          LastName: "",
          PhoneNumber: "",
          Username: "",
          Password: "",
        },
        editedDepartment: {},
        showAddPopup: false,
        showEditPopup: false,
        showDeletePopup: false,
        deletedept_id: null,
      };
    },
    methods: {
      fetchDepartments() {
        axios.get("http://localhost/ICT495_02_67/week12/department_api.php").then((response) => {
          if (response.data.success) {
            this.Departments = response.data.data;
          }
        });
      },
      addDepartment() {
        axios.post("http://localhost/ICT495_02_67/week12/department_api.php", this.newDepartment).then((response) => {
          if (response.data.success) {
            alert(response.data.message);
            this.fetchDepartments();
            this.newDepartment = {
              dept_name: "",
              LastName: "",
              PhoneNumber: "",
              Username: "",
              Password: "",
            };
            this.showAddPopup = false;
          }
        });
      },
      openEditPopup(Department) {
        this.editedDepartment = { ...Department };
        this.showEditPopup = true;
      },
      updateDepartment() {
        axios.put("http://localhost/ICT495_02_67/week12/department_api.php", this.editedDepartment).then((response) => {
          if (response.data.success) {
            alert(response.data.message);
            this.fetchDepartments();
            this.showEditPopup = false;
          }
        });
      },
      confirmDelete(dept_id) {
        this.deletedept_id = dept_id;
        this.showDeletePopup = true;
      },
      deleteDepartment(dept_id) {
        axios.delete("http://localhost/ICT495_02_67/week12/department_api.php", {
          data: { dept_id },
        }).then((response) => {
          if (response.data.success) {
            alert(response.data.message);
            this.fetchDepartments();
            this.showDeletePopup = false;
          }
        });
      },
    },
    mounted() {
      this.fetchDepartments();
    },
  };
  </script>
  
  <style>
  .popup-overlay {
    position: fixed;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    background: rgba(0, 0, 0, 0.5);
    display: flex;
    justify-content: center;
    align-items: center;
  }
  .popup {
    background: #fff;
    padding: 20px;
    border-radius: 8px;
    width: 300px;
    text-align: center;
  }
  table {
    width: 100%;
    border-collapse: collapse;
  }
  th, td {
    padding: 8px;
    text-align: left;
    border: 1px solid #ddd;
  }
  </style>
  