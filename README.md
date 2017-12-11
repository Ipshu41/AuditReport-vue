# AuditReport-vue
<template>
<v-app>
  <v-container>
<div class=' deep-purple lighten-4'>
<v-card light color ='deep-purple lighten-3'>
         <v-layout row>
          <v-flex xs18 sm14 md8 lg4>
            <v-select 
              label="Select database"
              v-bind:items="databases"
              v-model="selectedDatabase"              
              item-value="<ID>k__BackingField"
              item-text="<DBName>k__BackingField"   
              @change='GetTables()'   
            ></v-select>
          </v-flex>
<v-spacer></v-spacer>
                <v-flex xs18 sm14 md8 lg4>
            <v-select 
              label="Select Table"
              v-bind:items="Table"
              v-model="SelectTable"              
              item-value="TableName"
              item-text="TableName"
              :disabled="hasDB"
            ></v-select>
          </v-flex>
        </v-layout>
</v-card>
         <v-layout row>

       <v-flex xs16 sm12 md6 lg2>
        <v-dialog persistent v-model="modal" lazy full-width>
          <v-text-field slot="activator" label="Start Date" v-model="startDate" @focus="$v.startDate.$touch()" prepend-icon="event" readonly></v-text-field>
          <v-date-picker v-model="startDate" scrollable dark>
            <template scope="{ save, cancel }">
              <v-card-actions light color ='deep-purple lighten-3'>
                <v-btn flat primary @click.native="cancel()">Cancel</v-btn>
                <v-btn flat primary @click.native="save()">Save</v-btn>
              </v-card-actions>
            </template>
          </v-date-picker>
        </v-dialog>
      </v-flex>

      <v-flex xs16 sm12 m6 lg2 style="margin-left:50px">
       <v-dialog persistent v-model="modal2" lazy full-width>
          <v-text-field slot="activator" label="End Date" v-model="endDate" prepend-icon="event" readonly></v-text-field>
          <v-date-picker v-model="endDate" scrollable dark>
            <template scope="{ save, cancel }">
              <v-card-actions>
                <v-btn flat primary @click.native="cancel()">Cancel</v-btn>
                <v-btn flat primary @click.native="save()">Save</v-btn>
              </v-card-actions>
            </template>
          </v-date-picker>
        </v-dialog>
      </v-flex>
    </v-layout>
  <v-card light color ='deep-purple lighten-3'>
<v-layout row wrap>
          <v-flex xs20 sm18 md12 lg8> 
            <v-text-field
              name="input-1-3"
              label="Enter user name"
              single-line
              prepend-icon="person"
            ></v-text-field>
          </v-flex>
           <v-flex xs20 sm18 md12 lg8>
            <v-text-field 
            name='input-1-3'
            label='Enter row ID'
            type='number'
            single-line
            perpend-icon="ID"
            >
            </v-text-field>
          </v-flex>
        </v-layout>
  </v-card>
        
         <v-layout row wrap>
          <v-flex md12>
            <input type="radio" id="insert" value="Insert" v-model="operationType">
           <label for="insert">Insert</label>
           <input type="radio" id="delete" value="Delete" v-model="operationType">
           <label for="delete">Delete</label>
           <input type="radio" id="update" value="Update" v-model="operationType">
           <label for="update">Update</label>
          <br>
          <span>You Selected: {{ operationType }}</span>
          </v-flex>
        </v-layout>

        <v-layout row wrap>
           <v-flex sm6 md4 lg2 >
             <v-card light color ='deep-purple lighten-3'>
            <v-text-field
              name="input-1"
              label="Here"
              light
            ></v-text-field>
            <v-btn @click.native="Search()" color='deep-purple lighten-2'>Search</v-btn>
             </v-card>
          </v-flex>
          </v-layout>
          <v-data-table
          v-bind:headers='headers'
          :items='items'
          hide-actions
          >
          <template slot= 'items', scope='props'>
            <td>{{props.item.title}}</td>
            <td class= 'text-xs-right'>{{props.item.tablename}}</td>
            <td class= 'text-xs-right'>{{props.item.rowid}}</td>
            <td class= 'text-xs-right'>{{props.item.operation}}</td>
            <td class= 'text-xs-right'>{{props.item.occuredat}}</td>
            <td class= 'text-xs-right'>{{props.item.performedby}}</td>
            <td class= 'text-xs-right'>{{props.item.applicationid}}</td>
            <td class= 'text-xs-right'>{{props.item.addepartment}}</td>
            <td class= 'text-xs-right'>{{props.item.adlocation}}</td>
            <td class= 'text-xs-right'>{{props.item.fieldname}}</td>
            <td class= 'text-xs-right'>{{props.item.oldvalue}}</td>
            <td class= 'text-xs-right'>{{props.item.newvalue}}</td>
          </template>
          </v-data-table>
        </div> 
  </v-container>
</v-app>
</template>
<script>
import service from '@/service'

export default {
  mounted () {
    service.GetDatabases().then(data => {
      console.log('got back from db', data)
      this.databases = data
    })
  },
  computed: {
    hasDB () {
      return this.selectedDatabase === 0
    }
  },
  data () {
    return {
      modal: false,
      modal2: false,
      startDate: null,
      endDate: null,
      value: '9999',
      operationType: [],
      rowID: null,
      userName: [],
      selectedDatabase: 0,
      databases: [],
      SelectTable: null,
      searchString: [],
      Table: [],
      databaseID: null,
      headers: [
        {
          text: 'Title',
          align: 'left',
          value: 'title',
          sortable: false
        },
         { text: 'TableName', value: 'tablename' },
         { text: 'RowID', value: 'rowid' },
         { text: 'Operation', value: 'operation' },
         { text: 'OccuredAt', value: 'occuredat' },
         { text: 'PerformedBy', value: 'performedby' },
         { text: 'ApplicationID', value: 'applicationid' },
         { text: 'ADDepartment', value: 'addepartment' },
         { text: 'ADLocation', value: 'adlocation' },
         { text: 'FieldName', value: 'fieldname' },
         { text: 'OldValue', value: 'oldvalue' },
         { text: 'NewValue', value: 'newvalue' }
      ],
      items: []
    }
  },
  methods: {
    GetTables () {
      console.log('in get table')
      this.Table = []
      service.GetTables(this.selectedDatabase).then(data => {
        console.log('got back from table', data)
        this.Table = data
      })
    },
    Search () {
      let obj = {
        searchString: this.searchString,
        startDate: this.startDate,
        endDate: this.endDate,
        rowID: this.rowID,
        userName: this.userName,
        operationType: this.opertaionType,
        databaseID: this.databaseID,
        table: this.table

      }
      service.Search(obj).then(data => {
        console.log('got back from search', data)
        this.Table = data
      })
    }
  }
}
</script>
