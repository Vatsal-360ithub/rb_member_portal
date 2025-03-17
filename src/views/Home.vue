
<template>
	<div>
	  <h1>Member Performance Card</h1>
	  <!-- Input field for Member ID -->
	  <input
		v-model="memberId"
		type="text"
		placeholder="Enter Member ID"
	  />
	  <!-- Button to fetch performance data -->
	  <button @click="getPerformanceCard">Fetch Performance Card</button>
	  <!-- Render the generated performance card HTML -->
	  <div v-html="performanceCardHtml"></div>
	</div>
  </template>
  
  <script>
  export default {
	name: "PerformanceCard",
	data() {
	  return {
		memberId: "",
		performanceCardHtml: "",
	  };
	},
	methods: {
	  getPerformanceCard() {
		if (!this.memberId) {
		  alert("Please enter a Member ID");
		  return;
		}
		// Call Frappe backend method with the entered Member ID
		frappe.call({
		  method: "rb.rb.doctype.member.member.get_member_html_data",
		  args: {
			member_id: this.memberId,
		  },
		  callback: (r) => {
			if (r.message && r.message.status) {
			  // Generate HTML for the performance card using data from the response
			  this.performanceCardHtml = this.generateMemberHTML(
				r.message.current_bonus_points,
				r.message.active_score,
				r.message.old_score_summary
			  );
			} else {
			  frappe.msgprint("Failed to load member data.");
			}
		  },
		});
	  },
	  generateMemberHTML(bonusPoints, activeScore, activeScoreVariants) {
		// Begin generating the HTML content
		let htmlContent = `<div class="sales-order-details-editor">`;
		
		// Bonus Points Section
		htmlContent += `
		  <div style="font-size: 12px; font-weight: bold; color: #000; margin-top: 20px;">
			Bonus Points: <span style="font-size: 12px; color: #000">${String(bonusPoints)}</span>
		  </div>
		`;
		
		// Active Score Section
		htmlContent += `
		  <div style="font-size: 12px; font-weight: bold; color: #000; margin-top: 20px;">
			Active Score Lifetime Sum: <span style="font-size: 12px; color: #000">${String(activeScore)}</span>
		  </div>
		  <br>
		`;
		
		// Process old score summary variants if available
		if (activeScoreVariants && typeof activeScoreVariants === "object") {
		  const variantsArray = Object.entries(activeScoreVariants).map(([type, points]) => ({ type, points }));
		  
		  if (variantsArray.length > 0) {
			htmlContent += '<div style="display: grid; grid-template-columns: repeat(4, 1fr); gap: 20px;">';
			variantsArray.forEach((score) => {
			  if (score.type !== "Net Total") {
				htmlContent += `
				  <div style="display:flex; justify-content: space-between;">
					<div style="width:100%;">
					  <label>${score.type}</label>
					  <input type="text" readonly value="${String(score.points)}" style="width:100%;"/>
					</div>
				  </div>
				`;
			  }
			});
			htmlContent += "</div>"; // End grid container
		  }
		}
		
		htmlContent += "</div>"; // Close main div
		
		return htmlContent;
	  },
	},
  };
  </script>
  
  <style scoped>
  input {
	padding: 5px;
	margin: 10px 0;
  }
  button {
	padding: 5px 10px;
	margin-bottom: 20px;
  }
  .sales-order-details-editor {
	border: 1px solid #ccc;
	padding: 15px;
	margin-top: 20px;
  }
  </style>
  







<!-- <template>
  <div>
	<h1>Home Page</h1>
	<h4>Hello</h4>

	<button @click="$resources.ping.fetch()">Ping</button>
  </div>
</template> -->

<!-- <script>
export default {
  resources: {
	ping() {
	  return {
		method: "frappe.ping", // Method to call on backend
		onSuccess(d) {
		  alert(d);
		},
	  };
	},
  },
};
</script> -->
