<template>
<div>
    <div class="mb-3">
            <router-link type="submit" class="btn btn-primary" :to="{path: '/reservedBooks'}">Switch to card view</router-link>
        </div>
<vue-good-table
    :columns="columns"
    :rows="userBorrowedBooks"
    :search-options="{
        enabled: true
    }"
    :line-numbers="true"
    :pagination-options="{
        enabled: true,
        perPage: 7,
        perPageDropdown: [5, 7, 10],
        dropdownAllowAll: false,
    }"
    >
    <template v-slot:table-row="props">
      <span v-if="props.column.field === 'title'" class="title-cell">
        <span @click="getBook(props)">{{props.row.title}}</span>
      </span>
      <span v-if="props.column.field === 'author'">
        <span>{{props.row.author}}</span>
      </span>
      <span v-if="props.column.field === 'description'" class="book-table__description">
        <span>{{props.row.description}}</span>
      </span>
      <span v-if="props.column.field === 'year'">
        <span>{{props.row.year}}</span>
      </span>
      <span v-if="props.column.field === 'actions'">
        <mdb-dropdown end tag="li" class="nav-item">
            <mdb-dropdown-toggle right tag="a" navLink color="secondary-color-dark" slot="toggle" waves-fixed>
                <template #button-content>
                    <mdb-icon icon="ellipsis-h" class="mr-3" />
                </template>
            </mdb-dropdown-toggle>
            <mdb-dropdown-menu>
                <mdb-dropdown-item v-if="currentUser.data.id == user.data.id" @click.native="showModal=true;selectedBook=props.row;"><mdb-icon icon="trash" class="mr-3" />Remove</mdb-dropdown-item>
            </mdb-dropdown-menu>
        </mdb-dropdown>

      </span>
  </template>
</vue-good-table>
    <!-- <div>
    <mdb-modal centered v-if="showModal" @close="showModal = false">
      <mdb-modal-header>
        <mdb-modal-title>Warning</mdb-modal-title>
      </mdb-modal-header>
      <mdb-modal-body>Are you sure you want to delete selected book?</mdb-modal-body>
      <mdb-modal-footer>
        <mdb-btn color="primary" @click.native="showModal = false">Close</mdb-btn>
        <mdb-btn color="danger" @click.native="removeBook(selectedBook)">Delete</mdb-btn>
      </mdb-modal-footer>
    </mdb-modal>
  </div> -->

</div>
     
</template>

<script>
import { mapGetters } from 'vuex'
import { mdbDropdown, mdbDropdownItem, mdbDropdownMenu, mdbDropdownToggle, mdbIcon, mdbModal,
    mdbModalHeader,
    mdbModalTitle,
    mdbModalBody,
    mdbModalFooter,
    mdbBtn} from 'mdbvue';

import axios from 'axios'
export default {
    name: 'reservedListing',
    components: {
      mdbDropdown,
      mdbDropdownItem,
      mdbDropdownMenu,
      mdbDropdownToggle,
      mdbIcon,
      mdbModal,
      mdbModalHeader,
      mdbModalTitle,
      mdbModalBody,
      mdbModalFooter,
      mdbBtn
    },
    data(){
      return {
        user: JSON.parse(window.localStorage.getItem('user')),
        showModal: false,
        selectedBook: {},
        userBorrowedBooks: [],
        isMember: false,
        user: JSON.parse(window.localStorage.getItem('user')),
        columns: [
          {
            label: 'Title',
            field: 'title',
          },
          {
            label: 'Author',
            field: 'author',
          },
          {
            label: 'Description',
            field: 'description',
          },
          {
            label: 'Year',
            field: 'year',
          },
          {
            label: '',
            field: 'actions',
            sortable: false,
            width: '50px',
          },
          
        ],
      };
  },
  methods: {

        getBook(params){
            this.$router.push({name: 'bookDetails', params: {id: params.row._id}})
        },

        goToListing(){
            this.$router.push({path: '/booksList/listing'})
        },
        async getUserBooks(){
            const response = await axios.get(`http://localhost:8000/api/user/getBorrowed/${this.currentUser.data.id}`)
            this.userBorrowedBooks = response.data.books
        }
    },

    mounted() {
      this.getUserBooks()
    },

    computed: {
        ...mapGetters({
          currentUser: 'getUser'
        })
    },
}
</script>

<style scoped>
.more-options{
  transition: 0.3s;
}

.more-options:hover{
  border-radius: 5px;
  background: rgb(230, 230, 230);
  color:rgb(0, 0, 0);
  transition: 0.3s;
}

.title-cell{
    font-weight: 600;
    width: 100%;
    overflow: hidden;
    text-overflow: ellipsis;
    display: -webkit-box;
    -webkit-line-clamp: 3; /* number of lines to show */
    -webkit-box-orient: vertical;
}

.title-cell:hover{
  cursor: pointer;
  color: #2d96e0;
}

.book-table__description{
    font-weight: normal;
    width: 100%;
    overflow: hidden;
    text-overflow: ellipsis;
    display: -webkit-box;
    -webkit-line-clamp: 3; /* number of lines to show */
    -webkit-box-orient: vertical;
}
</style>