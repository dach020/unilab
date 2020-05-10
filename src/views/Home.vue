<template>
  <el-container>
    <el-dialog
      title="Product Details"
      :visible.sync="dialogVisible"
      width="40%">
      <el-form :model="form" :rules="rules" ref="form">
        <el-form-item label="Name" prop="name">
          <el-input v-model="form.name" autocomplete="off"></el-input>
        </el-form-item>
        <el-form-item label="Description" prop="description">
          <el-input v-model="form.description" autocomplete="off"  rows="6" type="textarea" ></el-input>
        </el-form-item>
        <el-form-item label="Category" prop="category">
          <el-select style="width: 100%" v-model="form.category" placeholder="Please select category">
            <el-option label="Category 1" value="Category 1"></el-option>
            <el-option label="Category 2" value="Category 2"></el-option>
            <el-option label="Category 3" value="Category 3"></el-option>
            <el-option label="Category 4" value="Category 4"></el-option>
          </el-select>
        </el-form-item>
      </el-form>
      <span slot="footer" class="dialog-footer">
        <el-button @click="dialogVisible = false">Cancel</el-button>
        <el-button type="primary" @click="onSubmit('form')">{{ createMode ? 'Create' : 'Update' }}</el-button>
      </span>
    </el-dialog>
    <el-main>
      <el-row style="margin-bottom: 48px;">
        <el-col :span="12">
          <div style="display: flex; flex-direction: row; align-items: center">
            <h1 style="margin-bottom: 0px; margin-right: 12px">My Products</h1>
            <el-button icon="el-icon-plus" circle @click="create"></el-button>
          </div>
        </el-col>
        <el-col :span="12">
          <el-button style="float: right;" type="primary" @click="signOut">Sign Out</el-button>
        </el-col>
      </el-row>
      <el-row>
        <el-table
          :data="tableData"
          style="width: 100%">
          <el-table-column
            prop="name"
            label="Product Name">
          </el-table-column>
          <el-table-column
            prop="description"
            label="Description">
          </el-table-column>
          <el-table-column
            prop="category"
            width="180"
            label="Category">
          </el-table-column>
          <el-table-column
            fixed="right"
            label="Operations"
            width="180">
            <template slot-scope="scope">
              <el-button @click="handleUpdate(scope.$index, scope.row)" type="text" size="small">Update</el-button>
              <el-button @click="handleRemove(scope.$index, scope.row)" type="text" size="small">Remove</el-button>
            </template>
          </el-table-column>
        </el-table>
      </el-row>
    </el-main>
  </el-container>
</template>

<script>
import router from '../router'
export default {
  data () {
    return {
      createMode: true,
      tableData: localStorage.getItem('products') ? JSON.parse(localStorage.getItem('products')) : [],
      form: {
        selectedIndex: null,
        name: '',
        description: '',
        category: ''
      },
      dialogVisible: false,
      rules: {
        name: [
          { required: true, message: 'This field is required', trigger: 'blur' }
        ],
        description: [
          { required: true, message: 'This field is required', trigger: 'blur' }
        ],
        category: [
          { required: true, message: 'Please select category', trigger: 'blur' }
        ]
      }
    }
  },
  methods: {
    onSubmit (formName) {
      this.$refs[formName].validate((valid) => {
        if (valid) {
          const obj = {
            name: this.form.name,
            description: this.form.description,
            category: this.form.category
          }

          if (this.createMode) {
            this.tableData.push(obj)
            localStorage.setItem('products', JSON.stringify(this.tableData))
            this.$message.success('Product has been successfully created')
          } else {
            const products = JSON.parse(localStorage.getItem('products'))
            products[this.selectedIndex].name = this.form.name
            products[this.selectedIndex].description = this.form.description
            products[this.selectedIndex].category = this.form.category
            this.tableData = products
            localStorage.setItem('products', JSON.stringify(products))
            this.$message.success('Product has been successfully updated')
          }

          this.form.name = ''
          this.form.description = ''
          this.form.category = ''
          this.form.selectedIndex = null
          this.dialogVisible = false
        } else {
          return false
        }
      })
    },
    create () {
      this.createMode = true
      this.dialogVisible = true
    },
    handleRemove (index) {
      this.$confirm('This will permanently delete selected product. Continue?', 'Confirmation to Remove', {
        confirmButtonText: 'OK',
        cancelButtonText: 'Cancel',
        type: 'warning'
      }).then(() => {
        const products = JSON.parse(localStorage.getItem('products'))
        products.splice(index, 1)
        this.tableData = products
        localStorage.setItem('products', JSON.stringify(products))
        this.$message({
          type: 'success',
          message: 'Product has been successfully removed'
        })
      })
    },
    handleUpdate (index, row) {
      this.createMode = false
      this.form.name = row.name
      this.form.description = row.description
      this.form.category = row.category
      this.selectedIndex = index
      this.dialogVisible = true
    },
    signOut () {
      localStorage.removeItem('accessToken')
      router.push({ name: 'Login' })
    }
  }
}
</script>
