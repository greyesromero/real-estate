<template>
	<div>
		
		<section id="appointments" >
			<v-container 
				v-if="loading"
				fluid 
				grid-list-md 
				class="px-2 ma-0" 
				:style="{width: $vuetify.breakpoint.lgAndUp ? '100%' : '100%'}">
				<div  class="center-container">
					<v-container fill-height>
						<v-layout align-center justify-center>
						<v-progress-circular
							:size="48"
							:width="4"
							color="primary lighten-1"
							indeterminate
						></v-progress-circular>
						</v-layout>
					</v-container>
				</div>
			</v-container>
			<v-container  v-if="!loading" px-8>
				
				<v-row>
						
					<!-- Info General -->
					<v-col cols="12">
						<!--v-toolbar class="mb-3">
							<v-text-field hide-details prepend-icon="mdi-magnify" single-line color="red lighten-2" v-model="search"></v-text-field>

							<v-spacer></v-spacer>
							<v-btn @click="sortBy('name')" text>
								Fecha
								<v-icon right>mdi-unfold-more-horizontal</v-icon>
							</v-btn>
							
						</v-toolbar-->
						<v-layout >
							<v-container fluid grid-list-xl class="pa-0">
								

								<v-tabs height="64px" color="primary" centered grow >
									<v-tab :class="[$vuetify.breakpoint.smAndDown ? 'caption' : 'body-1']">
										<v-icon left>mdi-account-outline</v-icon>
										Mis Citas - Cliente
									</v-tab>
									<v-tab :class="[$vuetify.breakpoint.smAndDown ? 'caption' : 'body-1']">
										<v-icon left>mdi-account-group-outline</v-icon>
										Mis Citas - Agente
									</v-tab>
									<v-tab-item>
										<v-toolbar class="mb-3 mt-3" style="background-color:transparent!important;box-shadow:none!important;">
											<h2 class="text-left headline-1 font-weight-regular text--darken-1 mt-5 mb-5">Total Citas: {{this.filteredClientAppointments.length}}</h2>
													
											<v-spacer></v-spacer>
											<span>
												<v-menu bottom right >
													<template v-slot:activator="{ on }">
														<v-btn outlined v-on="on">
															
															<span >{{ filters[selected_filter]['value']}}</span>
															<v-icon right>mdi-menu-down</v-icon>
														</v-btn>
													</template>
													
													<v-list>
														<v-list-item  v-for="(filter, index) in filters" :key="index"  @click.native="selectFilter(index)">
															<v-list-item-title>{{ filter['value'] }}</v-list-item-title>
														</v-list-item>
													</v-list>
												</v-menu>
											</span>
										</v-toolbar>
										<Appointment v-for="(client, index) in filteredClientAppointments" :type="'1'" :key="client.id" :appointment="client" :index="index" v-on:cancelAppointment="cancelAppointment($event)" v-on:cancelClientAppointment="cancelClientAppointment($event)">
										</Appointment>
									</v-tab-item>
									<v-tab-item>
										<v-toolbar class="mb-3 mt-3" style="background-color:transparent!important;box-shadow:none!important;">
											<h2 class="text-left headline-1 font-weight-regular text--darken-1 mt-5 mb-5">Total Citas: {{this.filteredAgentAppointments.length}} </h2>
													
											<v-spacer></v-spacer>
											<span>
												<v-menu bottom right >
													<template v-slot:activator="{ on }">
														<v-btn outlined v-on="on">
															
															<span >{{ filters_agent[selected_filter_agent]['value']}}</span>
															<v-icon right>mdi-menu-down</v-icon>
														</v-btn>
													</template>
													
													<v-list>
														<v-list-item  v-for="(filter, index) in filters_agent" :key="index"  @click.native="selectFilterAgent(index)">
															<v-list-item-title>{{ filter['value'] }}</v-list-item-title>
														</v-list-item>
													</v-list>
												</v-menu>
											</span>
										</v-toolbar>
										<Appointment v-for="(agent, index) in filteredAgentAppointments" :type="'2'" :key="agent.id" :appointment="agent" :index="index" v-on:cancelAppointment="cancelAppointment($event)" v-on:cancelClientAppointment="cancelClientAppointment($event)">
										</Appointment>
									</v-tab-item>								
								</v-tabs>
							</v-container>
						</v-layout>
					</v-col>
				</v-row>
			</v-container>
		</section>
</div>
</template>

<script>
import axios from 'axios'
import Appointment from '../components/Appointment.vue'
export default {
	components: {
		Appointment
	},
	data: () => ({
		client_appointments: [],
		agent_appointments: [],
		properties: true,
		selected_gym:0,
		search: '',
		array_providers:[],
		selected_filter: 0,
		selected_filter_agent: 0,
		loading: false,
		filters:[{
			id: 1,
			value: 'Todas'
		},
		{
			id: 2,
			value: 'Pasadas'
		},
		{
			id: 3,
			value: 'Pendientes'
		}],
		filters_agent:[{
			id: 1,
			value: 'Todas'
		},
		{
			id: 2,
			value: 'Pasadas'
		},
		{
			id: 3,
			value: 'Pendientes'
		}]
	
	}),
	computed: {
		getUser : function(){ 
			return this.$store.getters.getUser
		},
		filteredClientAppointments: function() {
			let today = new Date();
			let index = this.selected_filter
			let filtered = this.client_appointments
			if(index == 1){
				filtered = this.client_appointments.filter((d) => {
					return new Date(d.scheduled).getTime() < today.getTime();
				});
			}
			if(index == 2){
				filtered = this.client_appointments.filter((d) => {
					return new Date(d.scheduled).getTime() >= today.getTime();
				});
			}
		
			
			return filtered;
		},
		filteredAgentAppointments: function() {
			let today = new Date();
			let index = this.selected_filter_agent
			let filtered = this.agent_appointments
			if(index == 1){
				filtered = this.agent_appointments.filter((d) => {
					return new Date(d.scheduled).getTime() < today.getTime();
				});
			}
			if(index == 2){
				filtered = this.agent_appointments.filter((d) => {
					return new Date(d.scheduled).getTime() >= today.getTime();
				});
			}
		
			
			return filtered;
		}
	},
	methods: {
		
		sortBy(prop) {
			this.array_providers.sort((a, b) => a[prop] < b[prop] ? -1 : 1)
		},
		createProvider(data) {
			this.array_providers.push(data);
			
		},
		updateStatus(data){
			this.array_providers[data.index].active = data.state
		},
		selectFilter(index) {
			this.selected_filter = index;	
			
		
		
		},
		selectFilterAgent(index) {
			this.selected_filter_agent = index;	
		
		
		},
		cancelClientAppointment(index){
			this.client_appointments.splice(index, 1);
		},
		cancelAppointment(index){
			this.agent_appointments.splice(index, 1);
		}
	},
	created() {
	

	},
	mounted () {
		this.loading = true
		axios.get('https://hsrealestate-api.herokuapp.com/api/users/'+this.getUser.id+'/')
		.then(response => {
			this.client_appointments = response.data.client_appointments.filter(item => item.active == true )
			this.agent_appointments = response.data.agent_appointments.filter(item => item.active == true )
			this.loading = false
		})
		.catch(error => {
			this.loading = false
			console.log(error);
		})
		
		
	}
}
</script>
