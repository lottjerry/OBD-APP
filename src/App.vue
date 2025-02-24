<script setup>
import { useBluetooth } from '@vueuse/core';
import { ref } from 'vue';
import axios from 'axios'; // ✅ Import axios

let message = ref('');
let error = ref('');
let speed = ref('');
let speedError = ref('');
let logs = ref('');

const { isSupported, isConnected, device, requestDevice, server, disconnect } =
	useBluetooth({
		acceptAllDevices: true,
	});

let getHomeMessage = async () => {
	try {
		const response = await axios.get('https://obd-api.onrender.com/'); // ✅ Ensure this API is running
		message.value = response.data.message;
		error.value = '';
		speed.value = '';
		speedError.value = '';
		logs.value = '';
	} catch (err) {
		error.value = err.message; // ✅ Store the actual error message
		message.value = '';
		speed.value = '';
		speedError.value = '';
		logs.value = '';
	}
};

let getSpeed = async () => {
	try {
		const response = await axios.get('https://obd-api.onrender.com/data'); // ✅ Ensure this API is running
		speed.value = response.data.speed;
		speedError.value = response.data.error;
		error.value = '';
		message.value = '';
		logs.value = '';
	} catch (err) {
		error.value = err.message; // ✅ Store the actual error message
		speed.value = '';
		speedError.value = '';
		message.value = '';
		logs.value = '';
	}
};

let getLogs = async () => {
	try {
		const response = await axios.get('https://obd-api.onrender.com/logs'); // ✅ Ensure this API is running
		logs.value = response.data.logs;
		speed.value = '';
		speedError.value = '';
		message.value = '';
		message.value = '';
		error.value = '';
	} catch (err) {
		error.value = err.message;
		speed.value = '';
		speedError.value = '';
		message.value = '';
		message.value = '';
		logs.value = '';
	}
};
</script>

<template>
	<div>
		<div>
			<h1>Bluetooth Device Connection</h1>
			<button @click="requestDevice">Get Bluetooth Device</button>
			<div v-if="device">
				<h3>Device Information</h3>
				<p><strong>Name:</strong> {{ device.name }}</p>
				<p><strong>ID:</strong> {{ device.id }}</p>
				<p><strong>Connected:</strong> {{ server?.connected }}</p>
				<div v-if="services?.length">
					<h3>Available Services</h3>
					<ul>
						<li v-for="service in services" :key="service.uuid">
							{{ service.uuid }}
						</li>
					</ul>
				</div>
				<p v-if="!services">No Available Services</p>
			</div>
		</div>

		<div>
			<button @click="getHomeMessage">Get Data</button>
		</div>
		<div>
			<button @click="getSpeed()">Get Speed</button>
		</div>
		<div>
			<button @click="getLogs()">Get Logs</button>
		</div>
		<div>
			<h1 v-if="speed">Speed: {{ speed }}</h1>
			<h1 v-if="speedError" style="color: red">Error: {{ speedError }}</h1>
			<h1 v-if="error" style="color: red">Error: {{ error }}</h1>
		</div>

		<div>
			<h1 v-if="message">Message: {{ message }}</h1>
			<h1 v-if="error" style="color: red">Error: {{ error }}</h1>
		</div>

		<div>
			<div v-if="logs">
				<div v-for="(log, index) in logs" :key="index">
					<li>{{ log }}</li>
				</div>
			</div>
			<h1 v-if="error" style="color: red">Error: {{ error }}</h1>
		</div>
	</div>
</template>
