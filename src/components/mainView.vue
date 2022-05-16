<template>
<v-app>
    <v-container class="recap" style="color:#bd1569" ma='10' pa='4'>
      <v-row>
        <v-card class="balance">
          <v-card-title style="color:#bd1569">Balance</v-card-title>
          <v-card-text>{{tampilkan-total}}</v-card-text>
        </v-card >
        
        <v-spacer></v-spacer>

        <v-card class="debit">
          <v-card-title style="color:#bd1569">Income</v-card-title>
          <v-card-text>{{tampilkan-total}}</v-card-text>         
        </v-card >

        <v-spacer></v-spacer>

        <v-card class="credit">
          <v-card-title style="color:#bd1569">Expence</v-card-title>
          <v-card-text>{{tampilkan-total}}</v-card-text>
        </v-card>
      </v-row>
    </v-container>

    <v-data-table
    :headers="headers"
    :items="finances"
    sort-by="category"
    class="elevation-1"
  >
    <template v-slot:top>
      <v-toolbar
        flat
      >
        <v-toolbar-title>History</v-toolbar-title>
        <v-divider
          class="mx-4"
          inset
          vertical
        ></v-divider>
        <v-spacer></v-spacer>
        <v-dialog
          v-model="dialog"
          max-width="500px"
        >
          <template v-slot:activator="{ on, attrs }">
            <v-btn
              color="#bd1569"
              dark
              class="mb-2"
              v-bind="attrs"
              v-on="on"
            >
              New Activity
            </v-btn>
          </template>
          <v-card>
            <v-card-title>
              <span class="text-h5">New Activity</span>
            </v-card-title>

            <v-card>
        <v-card-text>
          <v-container>
            <v-row>
              <v-col cols="12">
                <v-text-field
                  v-model="desc"
                  type = 'text'
                  label="Transaction"
                  required
                ></v-text-field>
              </v-col>
              
              <v-col cols="12">
                <v-text-field
                  v-model="nominal"
                  type = 'number'
                  label="Nominal*"
                  required
                ></v-text-field>
              </v-col>

              <v-col
                cols="12"
                sm="6"
              >
                <v-select
                  :items="['Income', 'Expence']"
                  label="Income/Expence"
                  required
                  v-model="category"
                ></v-select>
              </v-col>

              <v-col
                cols="12"
                sm="6"
                md="4"
              >
                <v-dialog
                  ref="dialog"
                  v-model = "modal"
                  :return-value.sync="date"
                  persistent
                  width="290px"
                >
                  <template v-slot:activator="{ on, attrs }">
                    <v-text-field
                      v-model="date"
                      label="Date"
                      prepend-icon="mdi-calendar"
                      
                      v-bind="attrs"
                      v-on="on"
                    ></v-text-field>
                  </template>
                  <v-date-picker
                    v-model="date"
                    scrollable
                  >
                    <v-spacer></v-spacer>
                    <v-btn
                      text
                      color="#bd1569"
                      @click ="closeModal()"
                    >
                      Cancel
                    </v-btn>
                    <v-btn
                      text
                      color="#bd1569"
                      @click="$refs.dialog.save(date)"
                    >
                      OK
                    </v-btn>
                  </v-date-picker>
                </v-dialog>
              </v-col>
            </v-row>
          </v-container>
        </v-card-text>
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn
            @click="closeDialog"
          >
            Close
          </v-btn>
          <v-btn
            @click="addFinance()"
          >
            Save
          </v-btn>
        </v-card-actions>
      </v-card>
          </v-card>
        </v-dialog>
        <v-dialog v-model="dialogDelete" max-width="500px">
          <v-card>
            <v-card-title class="text-h5">Are you sure you want to delete this item?</v-card-title>
            <v-card-actions>
              <v-spacer></v-spacer>
              <v-btn color="blue darken-1" text @click="closeDelete">Cancel</v-btn>
              <v-btn color="blue darken-1" text @click="deleteFinance(item)">OK</v-btn>
              <v-spacer></v-spacer>
            </v-card-actions>
          </v-card>
        </v-dialog>
      </v-toolbar>
    </template>
    <template> 
      <v-icon
        small
        class="mr-2"
        @click="updateFinance(item)"
      >
        mdi-pencil
      </v-icon>
      <v-icon
        small
        @click="deleteFinance(item)"
      >
        mdi-delete
      </v-icon>
    </template>
  </v-data-table>
</v-app>
</template>
<script>
import gql from 'graphql-tag'
export default {
    name: 'homeView',
    data (){
      return{
        category: '',
        desc : '',
        nominal : null,
        date : '',        
      }
    },
     apollo:{
      finances :{
        query(){
          return gql`
          {
            finances: finance {
                id
                category
                activity
                nominal
                date
            }
          }
          `
        },
        subscribeToMore:{
          document: gql`
          subscription MySubscription {
            finance {
              id
              category
              activity
              nominal
              date
            }
          }
          `,
          updateQuery : (previousResult, {subscriptionData})=>{
            console.log("~finances~ Prev", previousResult)
            console.log('Subscription Data', subscriptionData)
            return{
              finances : subscriptionData.data.finance,
            }
          }
        }
      }
    },
    methods:{
      async addFinance(){
        console.log('Add Data ...');
        await this.$apollo.mutate({
          mutation: gql`
          mutation addFinance($category:String!, $activity:String!,$nominal: Int!,$date:date){
            insert_finance(
              objects :
                {
                  category: $category
                  activity :$activity
                  nominal:$nominal
                  date : $date
                }
              ){
                returning
                {id
                category
                activity
                nominal
                date}
              }
          }
          `,
          variables : {
            category : this.category,
            activity : this.desc,
            nominal : this.nominal,
            date : this.date
          }
        })
      },
      async deleteFinance(index){
        console.log('Delete Data ...')
        await this.$apollo.mutate({
          mutation: gql`
           mutation deleteFinance ($id: Int!){
             delete_finance_by_pk(id:$id){
               id
             }
           }
          `,
          variables:{
            id: index
          }
        })
      },
      updateFinance(index,value){
        if(this.edit==false){
          this.edit=true
          this.index_edit=index
          this.value_edit=value
        } else {
          console.log('Update Data ...')
          this.$apollo.mutate({
            mutation : gql`
            mutation updateFinance($id:Int!,credit: Int!, debit: Int!){
              update_finance_by_pk(
                pk_columns: {id:$id}
                _set: {debit: $debit, credit : $credit}
              ){
                id
              }
            }
            `,
            variables:{
              id: index,
              credit : this.nominal,
              debit : this.nominal
            }
          })
          this.edit=false
        }
      }
    },
    closeModal(){
      this.modal = false
    },
    closeDelete(){
      this.dialogDelete = false
    },
    closeDialog(){
      this.dialog = false
    }
    }


</script>
