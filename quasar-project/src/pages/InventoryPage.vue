<template>
  <div class="q-pa-md">
    <q-table
      flat
      bordered
      title="Current Inventory"
      :rows="rows"
      :columns="columns"
      row-key="id"
    >
      <template v-slot:body="props">
        <q-tr :props="props">
          <q-td key="name" :props="props">
            {{ props.row.name }}
          </q-td>
          <q-td key="stock" :props="props">
            {{ props.row.stock }}
            <q-popup-edit
              v-model="props.row.stock"
              title="Update stock"
              buttons
              v-slot="scope"
            >
              <q-input type="number" v-model="scope.value" dense autofocus />
            </q-popup-edit>
          </q-td>
          <q-td key="onorder" :props="props">
            <div class="text-pre-wrap">{{ props.row.onorder }}</div>
            <q-popup-edit
              v-model="props.row.onorder"
              title="Update on order"
              buttons
              v-slot="scope"
            >
              <q-input type="number" v-model="scope.value" dense autofocus />
            </q-popup-edit>
          </q-td>
        </q-tr>
      </template>
    </q-table>
  </div>
  <div style="display: flex; justify-content: center">
    <q-btn color="primary" label="Save" @click="save" />
    <q-btn color="primary" label="Logout" @click="logout" />
    <!--<q-btn color="primary" label="test" @click="test" />-->
  </div>
  <q-page q-page-container class="flex flex-center">
    <img
      alt="Nate's Furniture logo"
      src="~assets/Logo.png"
      style="width: 200px; height: 200px"
    />
  </q-page>
</template>

<script>
import { client } from "src/pages/directusclient.js";
import { logout, readItems, updateItems } from "@directus/sdk";
import { ref } from "vue";

const columns = [
  { name: "name", label: "Name", field: "name", align: "left" },
  { name: "stock", label: "Stock", field: "stock", align: "center" },
  { name: "onorder", label: "On Order", field: "onorder", align: "right" },
];

const rows = ref([]);

export default {
  setup() {
    return {
      columns,
      rows,
    };
  },

  async created() {
    // Load inventory data when the component is created (page loads)
    this.authenticate();
  },

  methods: {
    async authenticate() {
      const authToken = localStorage.getItem("authToken");
      if (!authToken) {
        // Redirect the user to the login page if no authtoken
        this.$router.push("/");
        return;
      }
      client.setToken(authToken);

      try {
        const response = await client.request(
          readItems("Inventory", {
            fields: ["*", { author: ["*"] }],
          })
        );
        //Debug stuff
        console.log("Data retrieved:", response);
        if (response) {
          rows.value = response;
        } else {
          console.log("No data found in the 'Inventory' collection");
        }
      } catch (error) {
        console.error("Error retrieving data:", error);
      }
    },

    async save() {
      for (const row of this.rows) {
        console.log(row.id);
        const response = await client.request(
          updateItems("Inventory", [row.id], {
            stock: row.stock,
            onorder: row.onorder,
          })
        );
        console.log(response);
      }
      this.$router.push("/home");
    },

    logout() {
      this.$router.push("/");
      localStorage.removeItem(client.authToken);
    },
  },
};
</script>
