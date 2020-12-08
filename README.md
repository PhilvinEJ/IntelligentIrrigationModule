<!DOCTYPE html>
<html>
	<body>

		<h1>IoT Plant</h1>


		<button type="button" onclick="publish('ON')">Auto Mode ON</button>
		<button type="button" onclick="publish('OFF')">Auto Mode OFF</button>
		<button type="button" onclick="publish('WATER')">Water Plant</button>

		<p id="sensorData">Temperature and Humidity Data</p>

		<div id="chart"></div>

		<script type="text/javascript" src="https://pubnub.github.io/eon/v/eon/1.0.0/eon.js"></script>
		<link type="text/css" rel="stylesheet" href="https://pubnub.github.io/eon/v/eon/1.0.0/eon.css"/>

		<script src="https://cdn.pubnub.com/sdk/javascript/pubnub.4.20.2.js"></script>

		<script>

		var pubnub = new PubNub({
			publishKey : 'pub-c-cb2e18e3-a8b0-486a-bf82-2d9e9f670b7e',
			subscribeKey : 'sub-c-a667485c-757f-11e8-9f59-fec9626a7085',
			});



			function publish(a){
				var publishConfig = 
				{
					channel : "ch1",
					message : a
				};

				pubnub.publish(publishConfig, function(status, response){
					console.log(status, response);
				});

			}
eon.chart({
			  channels: ['eon-chart'],
			  history: true,
			  flow: true,
			  pubnub: pubnub,
			  generate: {
			    bindto: '#chart',
			    data: {
			      labels: false
			    }
			  }
			});


// 		setInterval(function(){
//   pubnub.publish({
//     channel: 'eon-chart',
//     message: {
//       eon: {
//         'Austin': Math.floor(Math.random() * 99),
//         'New York': Math.floor(Math.random() * 99),
//         'San Francisco': Math.floor(Math.random() * 99),
//         'Portland': Math.floor(Math.random() * 99)
//       }
//     }
//   });

// }, 1000);

		pubnub.addListener({
		    message: function(m) {
		        // handle message
		        var channelName = m.channel; // The channel for which the message belongs
		        var channelGroup = m.subscription; // The channel group or wildcard subscription match (if exists)
		        var pubTT = m.timetoken; // Publish timetoken
		        var msg = m.message; // The Payload
		        var publisher = m.publisher; //The Publisher

		        document.getElementById("sensorData").innerHTML = msg;
		        console.log(msg)
		    },
		    presence: function(p) {
		        // handle presence
		        var action = p.action; // Can be join, leave, state-change or timeout
		        var channelName = p.channel; // The channel for which the message belongs
		        var occupancy = p.occupancy; // No. of users connected with the channel
		        var state = p.state; // User State
		        var channelGroup = p.subscription; //  The channel group or wildcard subscription match (if exists)
		        var publishTime = p.timestamp; // Publish timetoken
		        var timetoken = p.timetoken;  // Current timetoken
		        var uuid = p.uuid; // UUIDs of users who are connected with the channel
		    },
		    status: function(s) {
		        var affectedChannelGroups = s.affectedChannelGroups;
		        var affectedChannels = s.affectedChannels;
		        var category = s.category;
		        var operation = s.operation;
		    }
		});

		pubnub.subscribe({
		    channels: ['ch2'],

		});

		</script>


	</body>
</html>
