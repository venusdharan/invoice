<template>
    <div >
        <v-container>
            <v-row>
                <v-col>
                    <v-tabs
                    v-model="tab"
                    align-with-title
                    >
                    <v-tab>
                        View
                    </v-tab>
                    <v-tab>
                         Settings
                    </v-tab>
                    </v-tabs>
                    <v-tabs-items v-model="tab">
                    <v-tab-item>
                    <div class="invoice-box" id="element-to-print">
                    <table cellpadding="0" cellspacing="0">
                        <tr class="top">
                        <td colspan="5">
                            <table>
                            <tr>
                                <td class="title">
                                <img src="/compass_logo.png" style="width:100%; max-width:300px;">
                                
                                </td>

                                <td>
                                Invoice #: {{number}}<br><div v-if="date_start"> Created: {{date_start.toISOString().split('T')[0]}}</div><div v-if="date_due"><br> Due: {{date_due.toISOString().split('T')[0]}}</div>
                                </td>
                            </tr>
                            </table>
                        </td>
                        </tr>

                        <tr class="information">
                        <td colspan="5">
                            <table>
                            <tr>
                                <td>
                                Epahs building, YMCA Rd<br> above indusland bank,<br> Kozhikode, Kerala 673001
                                </td>

                                <td>
                                    {{name}}<br>{{address}}<br>{{email}}
                                </td>
                            </tr>
                            </table>
                        </td>
                        </tr>

                        <!--tr class="heading">
                        <td colspan="3">Payment Method</td>
                        <td>Check #</td>
                        </tr>

                        <tr class="details">
                        <td colspan="3">Check</td>
                        <td>1000</td>
                        </tr-->

                        <tr class="heading">
                        <td>Batch Code</td>
                        <td>Subject</td>
                        <td>Time</td>
                        <td>Fee / Hr</td>
                        <td>Amount</td>
                        </tr>

                        <tr class="item" v-for="item in items" :key="item">
                        <!--td><input v-model="item.description" /></td>
                        <td>$<input type="number" v-model="item.price" /></td>
                        <td><input type="number" v-model="item.quantity" /></td>
                        <td>${{ item.price * item.quantity | currency }}</td-->
                        <td>{{ item.code}}</td>
                                                <td>{{ item.subject }}</td>
                                                <td>{{ item.time }}</td>
                                                <td>{{ item.fee }}</td>
                                                <td>{{ item.amount }}</td>
                        </tr>

                        <!--tr>
                        <td colspan="4">
                            <button class="btn-add-row" >Add row</button>
                        </td>
                        </tr-->

                        <tr class="total">
                        <td colspan="3"></td>
                        <td>Total: &#x20B9;{{ total_am }}</td>
                        <tr>
                            <td>{{total_words.toUpperCase()}} <strong>INR</strong></td>
                        </tr>
                        
                        </tr>
                    </table>
                    </div>
                    </v-tab-item>
                    <v-tab-item>
                        <v-card>
                            <v-row>
                                <v-col>
                                    <v-card-text>
                                        <v-text-field label="Number" v-model="number"></v-text-field><br>
                                        <v-text-field label="Name" v-model="name"></v-text-field><br>
                                        <v-textarea label="Address" v-model="address"></v-textarea ><br>
                                        <v-text-field label="Email" v-model="email"></v-text-field>
                                        <input type="date"  :value="dateToYYYYMMDD(date_start)"
                                                @input="date_start = $event.target.valueAsDate" /><br>
                                        <input type="date"  :value="dateToYYYYMMDD(date_due)"
                                                @input="date_due = $event.target.valueAsDate" /><br>
                                 </v-card-text>
                                </v-col>
                                <v-col>
                                    <v-card-text>
                                        <v-simple-table>
                                            <template v-slot:default>
                                            <thead>
                                                <tr>
                                                <th class="text-left">
                                                    Batch Code
                                                </th>
                                                <th class="text-left">
                                                    Subject
                                                </th>
                                                <th class="text-left">
                                                    Time
                                                </th>
                                                <th class="text-left">
                                                    Fee/Hr
                                                </th>
                                                <th class="text-left">
                                                    Amount
                                                </th>
                                                <th class="text-left">
                                                    Delete
                                                </th>
                                                </tr>
                                            </thead>
                                            <tbody>
                                                <tr
                                                v-for="item in items"
                                                :key="item"
                                                >
                                                <td>{{ item.code}}</td>
                                                <td>{{ item.subject }}</td>
                                                <td>{{ item.time }}</td>
                                                <td>{{ item.fee }}</td>
                                                <td>{{ item.amount }}</td>
                                                <td><v-btn @click="remove_item(item)" text x-small>-</v-btn></td>
                                                </tr>
                                            </tbody>
                                            </template>
                                        </v-simple-table>
                                        <v-text-field label="Batch Code" v-model="item_batch_code"></v-text-field>
                                        <v-text-field label="Subject" v-model="item_subject"></v-text-field>
                                        <v-text-field label="Time" v-model="item_time"></v-text-field>
                                        <v-text-field label="Fee / Hr" v-model="item_fee"></v-text-field>
                                        <v-text-field label="Amount" v-model="item_amount"></v-text-field>
                                    </v-card-text>
                                    <v-card-actions>
                                        <v-btn @click="add_item">ADD</v-btn>
                                    </v-card-actions>
                                </v-col>
                            </v-row> 
                        </v-card>
                    </v-tab-item>
                </v-tabs-items>
                </v-col>
               
                </v-row>
                <v-row>
                <v-col>
                    <v-btn @click="generate_enquiry" text >Export to PDF</v-btn>
                </v-col>
                </v-row>
                
        </v-container>
    </div>
</template>

<script>
import numWords from 'num-words';
import html2pdf from 'html2pdf-jspdf2';
export default {
    data() {
        return {
            tab: null,
            number:"",
            name:"",
            phone:"",
            address:"",
            email:"",
            date_start:"",
            date_due:"",
            items:[],


            item_batch_code:"",
            item_subject:"",
            item_fee : 0 ,
            item_time : "",
            item_amount : 0,

            total_am:0,
            total_words:""

        };
    },
    methods: {
    add_item(){
        this.items.push(
            {
                code : this.item_batch_code,
                subject : this.item_subject,
                fee : this.item_fee,
                time : this.item_time,
                amount : this.item_amount
            }
        )
        this.get_total()
    },
    get_total(){
        var i = 0;
        for(var item in this.items){
            console.log(item)
            try {
                i = i + parseFloat(this.items[item].amount);
            } catch (error) {
                console.log(error)
            }
        }
        this.total_am = i;
        this.total_words = numWords(this.total_am );
    },
    remove_item(item){
        var i = this.items.indexOf(item);
        if(i != -1){
            this.items.splice(i, 1);
        }
         this.get_total()
    },
    dateToYYYYMMDD(d) {
      // alternative implementations in https://stackoverflow.com/q/23593052/1850609
    	return d && new Date(d.getTime()-(d.getTimezoneOffset()*60*1000)).toISOString().split('T')[0]
    },
    async generate_enquiry(){
      var element = document.getElementById('element-to-print')
            html2pdf(element, {
					margin: 1,
					filename: "class_attendance"+"hh" + '.pdf',
					image: { type: 'jpeg', quality: 0.98 },
					html2canvas: { dpi: 192, letterRendering: true },
					jsPDF: { unit: 'mm', format: 'A4', orientation: 'portrait' }
				})
    },
  },
}
</script>

<style scoped>
.invoice-box {

  margin: auto;
  padding: 30px;
    background-color: white;
  font-size: 16px;
  line-height: 24px;
  font-family: "Helvetica Neue", "Helvetica", Helvetica, Arial, sans-serif;
  color: #555;
}

.invoice-box table {
  width: 100%;
  line-height: inherit;
  text-align: left;
}

.invoice-box table td {
  padding: 5px;
  vertical-align: top;
}

.invoice-box table tr td:nth-child(n + 2) {
  text-align: right;
}

.invoice-box table tr.top table td {
  padding-bottom: 20px;
}

.invoice-box table tr.top table td.title {
  font-size: 45px;
  line-height: 45px;
  color: #333;
}

.invoice-box table tr.information table td {
  padding-bottom: 40px;
}

.invoice-box table tr.heading td {
  background: #eee;
  border-bottom: 1px solid #ddd;
  font-weight: bold;
}

.invoice-box table tr.details td {
  padding-bottom: 20px;
}

.invoice-box table tr.item td {
  border-bottom: 1px solid #eee;
}

.invoice-box table tr.item.last td {
  border-bottom: none;
}

.invoice-box table tr.item input {
  padding-left: 5px;
}

.invoice-box table tr.item td:first-child input {
  margin-left: -5px;
  width: 100%;
}

.invoice-box table tr.total td:nth-child(2) {
  border-top: 2px solid #eee;
  font-weight: bold;
}

.invoice-box input[type="number"] {
  width: 60px;
}

@media only screen and (max-width: 600px) {
  .invoice-box table tr.top table td {
    width: 100%;
    display: block;
    text-align: center;
  }

  .invoice-box table tr.information table td {
    width: 100%;
    display: block;
    text-align: center;
  }
}

/** RTL **/
.rtl {
  direction: rtl;
  font-family: Tahoma, "Helvetica Neue", "Helvetica", Helvetica, Arial,
    sans-serif;
}

.rtl table {
  text-align: right;
}

.rtl table tr td:nth-child(2) {
  text-align: left;
}
</style>