<template>
    <div>
    <v-card rounded="0" elevation="0" color="#f2f5f8"> 
            <v-breadcrumbs :items="items">
            <template v-slot:divider>
                <v-icon>mdi-chevron-right</v-icon>
            </template>
            </v-breadcrumbs>
        </v-card>
    <v-container>

        <!-- appointment list  -->
                   <v-row>
                       <v-col>
                           <v-card class="pa-4 mt-2" elevation="0" style="border: 1px solid #e7e7e7" width="100%">
                               <v-row class="pa-5">
                                   <v-icon large>mdi-timetable</v-icon> <h3 class="mt-1 ml-2">Todays Appointment</h3>   <v-spacer></v-spacer> <v-btn depressed @click="createAppDialog = true" color="info">Create Appointment</v-btn>
                               </v-row>
                               <v-row style="background-color:#f2f5f8;border-radius:8px;text-align:center">
                                   <v-col cols="3">
                                       <b>Name</b>
                                   </v-col>
                                   <v-col cols="1">
                                       <b>Age</b>
                                   </v-col>
                                   <v-col>
                                       <b>Phone Number</b>
                                   </v-col>
                                   <v-col>
                                       <b>Date</b>
                                   </v-col>
                                   <v-col>
                                       <b>Payment</b>
                                   </v-col>
                                     <v-col>
                                       <b>Status</b>
                                   </v-col>
                                   <v-col cols="2">
                                       <b>Action</b>
                                   </v-col>
                               </v-row>
                               <v-row align="center" v-for="appointment in appointmentList" :key="appointment.lId" :class="{ incomplete: (appointment.data.prescription == null || appointment.data.prescription.id == '')}" style="text-align:center;border-bottom: 1px solid #e7e7e7">
                                    <v-col class="ml-2" style="text-align:left" cols="3">

                                        <v-row justify="center" align="center">
                                            <v-col cols="4">
                                               <v-avatar
                                               class="white--text"
                                                :color="getRandomColor()"
                                                size="40"
                                                ><h3>{{appointment.data.patientName.charAt(0)}}</h3></v-avatar>
                                            </v-col>
                                            <v-col cols="8">
                                                 <h4 class="">
                                                    {{appointment.data.patientName}}
                                                </h4>   
                                            </v-col>
                                        </v-row>
                                    </v-col>
                                    <v-col cols="1">
                                        <v-chip small outlined color="teal">{{appointment.data.patientAge}}</v-chip>
                                    </v-col>
                                    <v-col>
                                        <v-card-subtitle>
                                            {{appointment.data.patientPhoneNo}}
                                        </v-card-subtitle>
                                    </v-col>
                                    <v-col>
                                        <v-card-subtitle>
                                            {{appointment.data.appointmentDate}}
                                        </v-card-subtitle>
                                    </v-col>
                                    <v-col>
                                        <v-chip class="white--text" small color="blue">{{appointment.data.paymentMethod}}</v-chip>
                                    </v-col>
                                    <v-col>
                                        <v-chip class="" small :color="getAppointmentStatusColor(appointment) ">{{getAppointmentStatus(appointment)}}</v-chip>
                                    </v-col>
                                    <v-col cols="2">
                                        <v-card-subtitle>
                                                 <v-btn-toggle>
                                                     <v-btn color="info" depressed small :disabled="appointment.data.prescription == null || appointment.data.prescription.id == ''" @click="showPrescription(appointment.data.id)"><v-icon style="color:white!important" small>mdi-eye</v-icon></v-btn>
                                                     <v-btn color="info" depressed small @click="createPrescription(appointment.data.id)"><v-icon style="color:white!important" small>mdi-prescription</v-icon></v-btn>
                                                </v-btn-toggle>
                                        </v-card-subtitle>
                                    </v-col>
                               </v-row>
                           </v-card>
                       </v-col>
                   </v-row>   

    <!-- dialog here -->

    <v-dialog title="Add New Drug" v-model="createAppDialog" max-width="800px">
        <v-card class="pa-6">
              <v-form
    ref="form"
    v-model="apoointmentFormModel"
    lazy-validation
  >
            <h3>Create New Appointment</h3>
                <v-row class="rowise pt-5">
                    <v-col>
                        <v-text-field
                        v-model="localAppointment.patientName"
                        placeholder="Name"
                        class="mt-2 pa-0"
                        :rules="[rules.required]"
                        outlined
                        dense
                        label="Patient Name"
                        >
                        <!-- <template #label>
    <span class="red--text"><strong>* </strong></span>Email
  </template> -->
                        </v-text-field>
                        <v-text-field
                        v-model="localAppointment.patientAge"
                        placeholder="Age"
                        class="mt-2 pa-0"
                        outlined
                        dense
                        :rules="[rules.required, rules.numOnly, rules.min]"
                        label="Patient Age"
                        >
                        </v-text-field>
                        <v-select
                        :items="genders"
                        label="Gender"
                        dense
                        outlined
                        :rules="[rules.required]"
                        ></v-select>
                         <v-text-field
                        v-model="localAppointment.patientPhoneNo"
                        placeholder="+880"
                        class="mt-2 pa-0"
                        outlined
                         :rules="[rules.required, rules.numOnly, rules.min]"
                        dense
                        label="Phone Number"
                        >
                        </v-text-field>
                         <v-text-field
                        v-model="localAppointment.patientAddress"
                        placeholder="Address"
                        class="mt-2 pa-0"
                        outlined
                         :rules="[rules.required]"
                        dense
                        label="Patient Address"
                        >
                        </v-text-field>
                    </v-col>
                    <v-col>
                        <v-menu
                        ref="menu"
                        :return-value.sync="localAppointment.appointmentDate"
                        :close-on-content-click="false"
                        transition="scale-transition"
                        offset-y
                        min-width="auto"
                    >
                        <template v-slot:activator="{ on, attrs }">
                        <v-combobox
                            v-model="localAppointment.appointmentDate"
                            :rules="[rules.required]"
                            label="Appointment Date"
                            prepend-icon="mdi-calendar"
                            readonly
                            v-bind="attrs"
                            v-on="on"
                        ></v-combobox>
                        </template>
                        <v-date-picker
                        v-model="localAppointment.appointmentDate"
                        no-title
                        >
                        <v-spacer></v-spacer>
                        <v-btn
                            text
                            color="primary"
                            @click="menu = false"
                        >
                            Cancel
                        </v-btn>
                        <v-btn
                            text
                            color="primary"
                            @click="$refs.menu.save(localAppointment.appointmentDate)"
                        >
                            OK
                        </v-btn>
                        </v-date-picker>
                    </v-menu>
                        <v-select
                        :items="paymentMethods"
                        label="Payment Method"
                        v-model="localAppointment.paymentMethod"
                        dense
                        outlined
                        ></v-select>
                        <v-textarea
                        style="margin-top:0px !important"
                        name="input-7-1"
                        outlined
                        background-color="white"
                        auto-grow
                        placeholder="Write the problem note..."
                        v-model="localAppointment.patientProblem"
                        ></v-textarea>
                    </v-col>
                    </v-row>
                    <v-row>
                        <v-col class="ma-0" style="text-align:right !important;">
                            <v-btn depressed @click="createAppointment" color="info"><v-icon class="mr-2">mdi-content-save</v-icon>Create</v-btn>
                        </v-col>
                    </v-row>
              </v-form>
              </v-card>
    </v-dialog>
            
    </v-container>
</div>  
</template>

<script>
import { initJsStore } from "@/service/idb_service.js";
import { ABService } from "@/service/Generic_Service.js";
import { v4 as uuidv4 } from 'uuid';
export default {
    async beforeCreate() {
    try {
      const isDbCreated = await initJsStore();
      if (isDbCreated) {
        console.log("db created");
      } else {
        console.log("db opened");
      }
    } catch (ex) {
      console.error(ex);
      alert(ex.message);
      Global.isIndexedDbSupported = false;
    }
  },
  data () {
    return {
        ABS: null,
        menu: false,
        uData: {},
        createAppDialog: false,
        genders: ["Male","Female"],
        paymentMethods: ["Cash","Online_Pay"],
        rules: {required: value => !!value || 'Required.',
        numOnly: value => /^[0-9]+$/.test(value) || 'Only numbers.',
        min:value => parseInt(value) >= 0 || 'Must be more than 0.',
        max: value => !!value || 'Must be less than 0.',},
        apoointmentFormModel: false,
        localAppointment: {
            // new Date().toJSON().slice(0,10)
                appointmentDate: "",
                createdOn: 0,
                gender: "Male",
                id: "",
                offline: true,
                paid: false,
                patientAddress: "",
                patientAge: "",
                patientName: "",
                patientPhoneNo: "",
                patientProblem: "",
                paymentMethod: "Cash",
                },
         appointment: {
                appointmentDate: "",
                createdAt: 0,
                createdBy: "",
                doctorsFee: 0,
                gender: "Male",
                id: "",
                isCompleted: false,
                isExpired: false,
                isPaid: false,
                otherFees: 0,
                patientAddress: "",
                patientAge: "",
                patientName: "",
                patientPhoneNo: "",
                patientProblem: "",
                paymentMethod: "Cash",
                prescription: {
                advice: [],
                bloodPressure: null,
                chiefComplaints: [],
                diagnosis: [],
                id: "",
                investigationAdvice: [],
                medicines: [],
                onExamination: [],
                pulse: null,
                temperature: null
                },
                totalFee: 0,
                updatedAt: 0,
                updatedBy: ""
            },
        
        appointmentList: [],
        items: [
            {
            text: 'a2sDMS',
            disabled: false,
            href: '/',
            },
            {
            text: 'Todays Appointment',
            disabled: true,
            href: 'create-appointment',
            },
        ]
    }
  },
  methods: {
      show () {
        return 0
    },
    showPrescription(appointmentId){
  window.open("/print/prescription/"+appointmentId,"_blank");
    },
    getAppointmentStatus(appointment){
      if(appointment.data.prescription == null || appointment.data.prescription.id == '')
      return "Incomplete";
      else  return "Complete";
    },
    getAppointmentStatusColor(appointment){
      if(appointment.data.prescription == null || appointment.data.prescription.id == '')
      return "error";
      else return "success";
    },
    getRandomColor() {
        // return 'rgb(' + 
        //     (Math.floor(Math.random()*56)+200) + ', ' +
        //     (Math.floor(Math.random()*56)+200) + ', ' +
        //     (Math.floor(Math.random()*56)+200) +
        //     ')';
        return "#"+Math.floor(Math.random()*16777215).toString(16);
     },
    async createAppointment(){
       if(!this.$refs.form.validate())
        return;

        this.localAppointment.id = uuidv4();
        this.localAppointment.createdOn = Date.now();
        let r = await this.ABS.addData("LocalAppointment", {
            id: this.localAppointment.id,
            data: this.localAppointment
        });
        if(r) console.log(r);
        this.setAppointment();
        this.createAppDialog = false
        this.getAppointmentList();
    },
    async setAppointment(){
        let data = this.appointment;
        data.id = this.localAppointment.id
        data.appointmentDate = this.localAppointment.appointmentDate
        data.createdAt = this.localAppointment.createdOn
        data.createdBy = "u"+this.uData.phoneNo
        data.updatedAt = this.localAppointment.createdOn
        data.updatedBy = "u"+this.uData.phoneNo
        data.doctorsFee = 400
        data.gender = this.localAppointment.gender
        data.otherFees = 0
        data.patientAddress = this.localAppointment.patientAddress
        data.patientAge = this.localAppointment.patientAge
        data.patientName = this.localAppointment.patientName
        data.patientPhoneNo = this.localAppointment.patientPhoneNo
        data.patientProblem = this.localAppointment.patientProblem
        data.paymentMethod = this.localAppointment.paymentMethod

        let r = await this.ABS.addData("Appointment", {
            id: data.id,
            data: data
        });
        if(r) console.log(r);
    },
    async getAppointmentList(){
        let data = await this.ABS.getData("Appointment");
        console.log(data)
        this.appointmentList = data;
    },
    createPrescription(id){
        localStorage.setItem("selectedAppointment",id);
        this.$router.push("/rx-prescription")
    }
  },
  mounted(){
       this.ABS = new ABService();
       this.getAppointmentList();
       this.uData = JSON.parse(localStorage.getItem("uData"));
  }
}
</script>
<style scoped>
.incomplete{
    background-color: #ffe7e7;
}
</style>
