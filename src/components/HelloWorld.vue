<template>
  <v-app id="inspire">
    <v-app-bar dark color="black " dense > <v-toolbar-title >Vehicle Tracking System </v-toolbar-title>
<v-spacer></v-spacer>


</v-app-bar>
    <v-card flat>
      <v-snackbar v-model="snackbar" absolute top right color="success">
        <span>Added successfully!</span>
        <v-icon dark>mdi-checkbox-marked-circle</v-icon>
      </v-snackbar>
      <v-flex sm4 offset-sm4 align-center justify-center>
        <v-card class="elevation-12 rounded-lg mt-10 mb-10" flat>
          <v-toolbar dark color="black">
            <v-toolbar-title>Entry Form</v-toolbar-title>
          </v-toolbar>
          <v-form ref="form" @submit.prevent="submit" align-center>
            <v-container fluid>
              <v-row>
                <v-col cols="12" sm="10">
                  <v-text-field
                    v-model="form.hiredate"
                    :min="nowDate"
                    color="black"
                    label="Hire Date"
                    type="date"
                    required
                    outlined
                  ></v-text-field>
                  
                  <v-text-field
                    :rules="rules.vehiclenumber"
                    v-model="form.vehiclenumber"
                    color="black"
                    label="Vehicle Number"
                    type="string"
                    required
                    outlined
                  ></v-text-field>
                  <v-text-field
                    v-model="form.cost"
                    :rules="rules.cost"
                    color="black"
                    label="Cost"
                    type="number"
                    required
                    outlined
                  ></v-text-field>
                </v-col>
              </v-row>
            </v-container>
            <v-card-actions>
              <v-btn text @click="resetForm">Cancel</v-btn>
              <v-spacer></v-spacer>
              <v-btn :disabled="!formIsValid" text color="black" type="submit">Submit</v-btn>
            </v-card-actions>
          </v-form>
        </v-card>
      </v-flex>
      <p class="font-weight-bold text-center text-uppercase text-h3 pt-9">
        Dashboard
      </p>
      <v-flex sm8 offset-sm2 mt-10>
      <v-card class="elevation-12 ">
      <v-tabs
        color="black"
        right
      >
        <v-tab>All Details</v-tab>
        <v-tab>Summary</v-tab>
        
          <v-tab-item>
          <v-simple-table>
      <template v-slot:default>
        <thead>
          <tr>
            <th class="text-left">
              HireDate
            </th>
            <th class="text-left">
              Vehicle Number
            </th>
            <th class="text-left">
              Cost
            </th>
            <th class="text-left">
              Options
            </th>
          </tr>
        </thead>
        <tbody>
          <tr
            v-for="item in entries"
            :key="item.id"
          >
            <td>{{ item.hiredate }}</td>
            <td>{{ item.vehiclenumber }}</td>
            <td>{{ item.cost }}</td>
            <td><v-btn @click="fireDelete(item.id)" ><v-icon color="red">mdi-delete</v-icon></v-btn></td>
          </tr>
        </tbody>
      </template>
    </v-simple-table>
        </v-tab-item>
        <v-tab-item>
           <v-simple-table>
      <template v-slot:default>
        <thead>
          <tr>
            <th class="text-left">
              HireDate
            </th>
            <th class="text-left">
              Total Vehicles
            </th>
            <th class="text-left">
              Total Cost
            </th>
          </tr>
        </thead>
        <tbody>
          <tr
            v-for="(item,name) in object"
            :key="name"
          >
            <td>{{name}}</td>
            <td v-for="it in item" :key="it">{{it}}</td>
          </tr>
        </tbody>
      </template>
    </v-simple-table>
        </v-tab-item>
  
        
      </v-tabs>
    </v-card>
    </v-flex>
    </v-card>
  </v-app>
</template>

<script>
export default {
  name: "HelloWorld",

  data() {
    const defaultForm = Object.freeze({
      id:null,
      hiredate:Date(),
      vehiclenumber:"",
      cost:null

    });

    return {
      i:0,
      object:{},
      nowDate: new Date().toISOString().slice(0,10),
      form: Object.assign({}, defaultForm),
      rules: {
        
        cost: [val => (val > 0) || "Cost has to be grater than 0"],
        vehiclenumber: [val => (/^[A-Z]{2}\s[0-9]{2}\s[A-Z]*\s[0-9]{4}$/.test(val)) || "Please enter correct format of vehicle number"]
      },
      snackbar: false,
      entries:[],

      defaultForm
    };
  },

  computed: {
    formIsValid() {
      return (
        this.form.cost && 
        this.form.vehiclenumber &&
        this.form.hiredate
        
      );
    }
  },

  methods: {
    resetForm() {
      this.form = Object.assign({}, this.defaultForm);
      this.$refs.form.reset();
    },
    submit() {
      this.snackbar = true;
      this.form.id=this.i++
      this.entries.push(this.form);
      this.calculateSummary();
      this.resetForm();
      
    },
    calculateSummary(){
      this.object={}
      this.entries.forEach(obj =>{
        if(!this.object[obj.hiredate]){
          this.object[obj.hiredate]={
            totalVehicles:0,
            totalCost:0
          }
        }
        let thisDate=this.object[obj.hiredate];
        thisDate.totalVehicles++;
        thisDate.totalCost+=parseInt(obj.cost)
      })
      console.log(this.object)
      
    },
    fireDelete(val){
      this.entries.splice(val,1);
      this.calculateSummary();
    }
    
  }
};
</script>
