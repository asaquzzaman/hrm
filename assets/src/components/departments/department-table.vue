<template>
    <div id="hrm-department-tbl-wrap">
        <table v-if="isFetchRecord" id="hrm-data-table" class="widefat">
                    
            <thead>
                <tr role="row">
                    <th v-if="manageDepartment()" class="sorting_asc">
                        <input @change.prevent="delAllDept()" class="hrm-all-checked" v-model="del_all_dept" type="checkbox">
                    </th>
                    <th class="sorting">
                        Department Title
                    </th>
                    <th class="sorting" >
                        Department Description
                    </th>
                    <th class="sorting">
                        Status
                    </th>
                    <th class="sorting">
                        No. of Employee
                    </th>
                </tr>
            </thead>

            <tbody>
                                        
                                                        
                                                        
                <tr class="hrm-even hrm-tr odd" role="row" v-for="department in departments">
            
                    <td v-if="manageDepartment()" class="hrm-table-checkbox sorting_1">
                        <input @change.prevent="delDept(department)" v-model="del_dept" class="hrm-single-checked" name="" :value="department.id" type="checkbox">
                    </td>

                    <td>
                        <div v-if="manageDepartment()" class="hrm-title-wrap">
                            <div class="hrm-editable hrm-title"><span v-html="department.hierarchical_pad"></span><span>{{ department.name }}</span></div>
                            <div class="hrm-title-action row-actions">
                                <department-edit-btn :department_id="department.id"></department-edit-btn>
                                <department-del-btn :department_id="department.id" :type="'single'"></department-del-btn>
                                <span class="hrm-clear"></span>
                            </div>
                        </div>

                        <div v-else class="hrm-title-wrap">
                            <span class="hrm-editable hrm-title" v-html="department.hierarchical_pad"></span><span>{{ department.name }}</span>
                        </div>

                    </td>

                    <td>{{ department.description }}</td>

                    <td>{{ departmentActivity(department) }}</td>
                    <td>{{ department.number_of_employee }}</td>

                </tr>

                <tr v-if="!departments.length">
                    <td colspan="5">
                        No record found!
                    </td>
                </tr>
                

            </tbody>
        </table>
    </div>
</template>
<script>
    import EditBtn from './department-edit-btn.vue';
    import DelBtn from './department-del-btn.vue';
    import Mixin from './mixin'
    
    export default {
        mixins: [Mixin],

        data: function() {
            return {
                del_all_dept: false,
                del_dept: []
            }
        },

        created () {
            this.getDepartments();
        },

        computed: {
            departments: function() {
                return this.$store.state.departments.departments;
            }
        },

        components: {
            'department-edit-btn': EditBtn,
            'department-del-btn': DelBtn
        },

        methods: {
            departmentQuery () {
                var self = this;
                
                var conditions = {
                    show_all: true
                };

                var args = {
                    data: conditions,
                    callback: function(res){
                        
                    
                    }  
                }

                this.getDepartments(args);
            },
            delAllDept: function() {
                if (this.del_all_dept) {
                    var depts_id = [];

                    this.$store.state.departments.departments.map(function(department) {
                        depts_id.push(department.id);
                    });

                    this.del_dept = depts_id;

                } else {
                    this.del_dept = [];
                }

                this.$store.commit('departments/departmentDelId', {del_dept: this.del_dept});
            },

            delDept: function() {
                var depts_id = [];

                this.$store.state.departments.departments.map(function(department) {
                    depts_id.push(department.id);
                });

                if (depts_id.length == this.del_dept.length) {
                    this.del_all_dept = true;
                }

                if (depts_id.length != this.del_dept.length) {
                    this.del_all_dept = false;
                }
                
                this.$store.commit('departments/departmentDelId', {del_dept: this.del_dept});
            },

            departmentActivity: function(department) {
                return parseInt(department.active) == 0 ? 'Disable' : 'Enable'; 
            }
        }
    }

</script>
