<template>
  <div>
    <table>
      <thead>
        <tr>
          <th>Customer ID</th>
          <th>Name</th>
          <th>Age</th>
          <th>Phone</th>
          <th>Action
            <button class="btn-add" @click="toggleAddForm">Add</button>
          </th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="customer in customers" :key="customer.customer_ID">
          <td>{{ customer.customer_ID }}</td>
          <td>{{ customer.Name }}</td>
          <td>{{ customer.Age }}</td>
          <td>{{ customer.phone }}</td>
          <td>
            <button @click="deleteCustomer(customer.customer_ID)">Delete</button>
            <button class="btn-update" @click="editCustomer(customer)">Update</button>
          </td>
        </tr>
      </tbody>
    </table>

    <div v-if="showAddForm || isUpdateForm" class="form-container">
      <h3>{{ isUpdateForm ? 'Update' : 'Add New' }} Customer</h3>
      <form @submit.prevent="isUpdateForm ? updateCustomer() : addCustomer()">
        <div class="form-group">
          <label for="customer_ID">Customer ID:</label>
          <input type="text" v-model="newCustomer.customer_ID" required :readonly="isUpdateForm">
        </div>
        <div class="form-group">
          <label for="Name">Name:</label>
          <input type="text" v-model="newCustomer.Name" required>
        </div>
        <div class="form-group">
          <label for="Age">Age:</label>
          <input type="number" v-model="newCustomer.Age" required>
        </div>
        <div class="form-group">
          <label for="phone">Phone:</label>
          <input type="text" v-model="newCustomer.phone" required>
        </div>
        <button type="submit" class="btn">{{ isUpdateForm ? 'Update' : 'Add' }} Customer</button>
      </form>
    </div>
  </div>
</template>

<script>
import axios from 'axios';
import { ref, onMounted } from 'vue';

export default {
  setup() {
    const customers = ref([]);
    const showAddForm = ref(false);
    const isUpdateForm = ref(false);
    const newCustomer = ref({
      customer_ID: '',
      Name: '',
      Age: '',
      phone: '',
    });

    const fetchCustomers = async () => {
      try {
        const response = await axios.get('http://localhost:5000/api/customers', {
          headers: {
            'Content-Type': 'application/json',
          },
        });
        customers.value = response.data.list; // Ensure we access 'list' property
      } catch (err) {
        console.error('Error fetching data:', err);
      }
    };

    const deleteCustomer = async (customerId) => {
      try {
        const response = await axios.delete(`http://localhost:5000/${customerId}`, {
          headers: {
            'Content-Type': 'application/json',
          },
        });
        console.log('Delete response:', response);
        // Update the customer list after deletion
        fetchCustomers();
      } catch (err) {
        console.error('Error deleting customer:', err);
      }
    };

    const addCustomer = async () => {
      try {
        const response = await axios.post('http://localhost:5000/add', newCustomer.value, {
          headers: {
            'Content-Type': 'application/json',
          },
        });
        console.log('Add response:', response);
        // Clear the form fields
        newCustomer.value = {
          customer_ID: '',
          Name: '',
          Age: '',
          phone: '',
        };
        showAddForm.value = false;
        // Update the customer list after adding
        fetchCustomers();
      } catch (err) {
        console.error('Error adding customer:', err);
      }
    };

    const editCustomer = (customer) => {
      newCustomer.value = { ...customer };
      showAddForm.value = true;
      isUpdateForm.value = true;
    };

    const updateCustomer = async () => {
      try {
        const response = await axios.put(`http://localhost:5000/update/${newCustomer.value.customer_ID}`, newCustomer.value, {
          headers: {
            'Content-Type': 'application/json',
          },
        });
        console.log('Update response:', response);
        // Clear the form fields
        newCustomer.value = {
          customer_ID: '',
          Name: '',
          Age: '',
          phone: '',
        };
        showAddForm.value = false;
        isUpdateForm.value = false;
        // Update the customer list after updating
        fetchCustomers();
      } catch (err) {
        console.error('Error updating customer:', err);
      }
    };

    const toggleAddForm = () => {
      showAddForm.value = !showAddForm.value;
      isUpdateForm.value = false;
      if (!showAddForm.value) {
        newCustomer.value = {
          customer_ID: '',
          Name: '',
          Age: '',
          phone: '',
        };
      }
    };

    onMounted(() => {
      fetchCustomers();
    });

    return {
      customers,
      showAddForm,
      isUpdateForm,
      newCustomer,
      deleteCustomer,
      addCustomer,
      updateCustomer,
      editCustomer,
      toggleAddForm,
    };
  },
};
</script>

<style scoped>
table {
  width: 100%;
  border-collapse: collapse;
}

th, td {
  border: 1px solid #ddd;
  padding: 8px;
}

th {
  background-color: #f2f2f2;
  text-align: left;
}

tbody tr:nth-child(even) {
  background-color: #f9f9f9;
}

button {
  margin-left: 5px;
  padding: 5px 10px;
  background-color: #ff0000;
  color: #fff;
  border: none;
  cursor: pointer;
}
.btn-add{
    margin-left: 5px;
  padding: 5px 10px;
  background-color: #72f47d;
  color: #fff;
  border: none;
  cursor: pointer;
}
btn-update{
     margin-left: 5px;
  padding: 5px 10px;
  background-color: #4f75c2;
  color: #fff;
  border: none;
  cursor: pointer;
}

button.update {
  background-color: #4CAF50;
}

.form-container {
  margin-top: 20px;
  padding: 20px;
  background-color: #f2f2f2;
  border: 1px solid #ddd;
  border-radius: 5px;
}

form h3 {
  margin-bottom: 20px;
}

.form-group {
  margin-bottom: 15px;
}

.form-group label {
  display: inline-block;
  width: 100px;
  font-weight: bold;
}

.form-group input {
  padding: 5px;
  width: calc(100% - 120px);
  border: 1px solid #ddd;
  border-radius: 3px;
}

.btn {
  background-color: #4CAF50;
  color: white;
  padding: 10px 20px;
  border: none;
  cursor: pointer;
  border-radius: 3px;
}

.btn:hover {
  background-color: #45a049;
}
</style>
