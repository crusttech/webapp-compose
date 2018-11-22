<template>
  <div class="container">
    <div class="row">
      <div class="col-md-12">
        <div class="well">
          <h2>Edit page</h2>
          <div v-if="editPageError" style="color:red;">
            {{ editPageError }}
          </div>
          <form v-if="!editPageError" @submit.prevent="handleEditPageFormSubmit">
            <input required type="hidden" v-model="editPageFormData.id" id="id" />
            <div class="form-group">
              <label for="title">Page title</label>
              <input required type="text" v-model="editPageFormData.title" class="form-control" id="title" placeholder="Page title" />
            </div>
            <div class="form-group">
              <label for="title">Description</label>
              <textarea v-model="editPageFormData.description" class="form-control" id="description" placeholder="Page description" />
            </div>
            <div class="form-group form-check">
              <input type="checkbox" id="visible" class="form-check-input" v-model="editPageFormData.visible">
                <label for="visible" class="form-check-label">Visible ?</label>
              </div>
              <div class="form-group">
                <label for="module">Module</label>
                <select v-model="editPageFormData.moduleID" class="form-control" id="module">
                  <option :value="null"></option>
                  <option v-for="module in modulesList" :key="module.id" :value="module.id">{{ module.name }}</option>
                </select>
                <router-link :to="{name: 'admin.modules'}" class="actions__action">Add a module</router-link>
              </div>
              <button type="submit" class="btn btn-primary">Save</button>
              <div v-if="editPageFormSubmitError" style="color:red;">
                {{ editPageFormSubmitError }}
              </div>
            </form>
          </div>
        </div>
      </div>
    </div>
</template>

<script>
export default {
  name: 'PageEdit',
  props: {
    pageID: {
      type: String,
      required: true,
    },
  },

  data () {
    return {
      editPageError: '',
      editPageFormSubmitError: '',
      modulesList: [],
      editPageFormData: {
        title: '',
        moduleID: '',
      },
    }
  },
  async created () {
    try {
      this.editPageError = ''
      this.editPageFormData = await this.$crm.pageRead({ pageID: this.pageID })
      // Parent pages : not itself
      this.modulesList = await this.$crm.moduleList({})
    } catch (e) {
      this.editPageError = 'Error when trying to init page form.'
    }
  },
  methods: {
    async handleEditPageFormSubmit () {
      try {
        this.editPageFormSubmitError = ''

        const payload = Object.assign({}, this.editPageFormData, { pageID: this.editPageFormData.id })
        delete payload.id

        await this.$crm.pageEdit(payload)
      } catch (e) {
        this.editPageFormSubmitError = 'Error when trying to edit page.'
      }
    },
  },
}
</script>