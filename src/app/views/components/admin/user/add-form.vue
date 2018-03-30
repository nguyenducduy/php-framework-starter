<lang src="./lang.yml"></lang>

<template>
  <div class="panel-body">
    <el-col :md="5" :xs="24">
      <h2>{{ $t('info.add.description') }}</h2>
      <p>{{ $t('info.add.extraDescription') }}</p>
    </el-col>
    <el-col :md="19" :xs="24">
      <el-col :md="10">
        <el-form autoComplete="on" label-position="left" class="create-form" :model="form" :rules="rules" ref="addForm">
          <el-form-item prop="fullname" :label="$t('label.name')">
            <el-input type="text" size="small" v-model="form.fullname"></el-input>
          </el-form-item>
          <el-form-item prop="email" :label="$t('label.email')">
            <el-input type="text" size="small" v-model="form.email"></el-input>
          </el-form-item>
          <el-form-item prop="password" :label="$t('label.password')">
            <el-input type="password" size="small" v-model="form.password"></el-input>
          </el-form-item>
          <el-form-item prop="groupid" :label="$t('label.group')">
            <el-select size="small" v-model="form.groupid" :placeholder="$t('label.selectGroup')" style="width: 100%" :loading="loading">
              <el-option v-for="item in formSource.groupList" :key="item" :label="item" :value="item">
              </el-option>
            </el-select>
          </el-form-item>
          <el-form-item prop="status" :label="$t('label.status')">
            <el-select size="small" v-model="form.status" :placeholder="$t('label.selectStatus')" style="width: 100%" :loading="loading">
              <el-option v-for="item in formSource.statusList" :key="item.label" :label="item.label" :value="item.value">
              </el-option>
            </el-select>
          </el-form-item>
          <el-form-item prop="verifytype" :label="$t('label.verifyType')">
            <el-select size="small" v-model="form.verifytype" :placeholder="$t('label.selectVerifyType')" style="width: 100%" :loading="loading">
              <el-option v-for="item in formSource.verifyList" :key="item.label" :label="item.label" :value="item.value">
              </el-option>
            </el-select>
          </el-form-item>
          <el-form-item style="margin-top: 30px">
            <el-button type="primary" :loading="loading" @click.native.prevent="onSubmit"> {{ $t('default.add') }}
            </el-button>
            <el-button @click="onReset">{{ $t('default.reset') }}</el-button>
          </el-form-item>
        </el-form>
      </el-col>
    </el-col>
  </div>
</template>

<script lang="ts">
import { Vue, Component, Watch } from 'nuxt-property-decorator';
import { Action, State } from 'vuex-class';

@Component
export default class AddForm extends Vue {
  @Action('users/get_form_source') formsourceAction;
  @Action('users/add') addAction;
  @State(state => state.users.formSource) formSource;
  @Watch('$route')
  onPageChange() { this.initData(); };

  loading: boolean = false;
  form: object = {
    email: '',
    password: '',
    fullname: '',
    groupid: '',
    status: '',
    verifytype: ''
  };

  $refs: {
    addForm: HTMLFormElement
  }

  get rules() {
    return {
      fullname: [
        {
          required: true,
          message: this.$t('msg.nameIsRequired'),
          trigger: 'blur'
        }
      ],
      email: [
        {
          required: true,
          message: this.$t('msg.emailIsRequired'),
          trigger: 'blur'
        },
        {
          type: 'email',
          message: this.$t('msg.emailInvalid'),
          trigger: 'blur,change'
        }
      ],
      password: [
        {
          required: true,
          message: this.$t('msg.passwordIsRequired'),
          trigger: 'blur'
        }
      ],
      groupid: [
        {
          required: true,
          message: this.$t('msg.groupIsRequired'),
          trigger: 'change'
        }
      ],
      status: [
        {
          required: true,
          message: this.$t('msg.statusIsRequired'),
          trigger: 'change'
        }
      ],
      verifytype: [
        {
          required: true,
          message: this.$t('msg.verifyTypeIsRequired'),
          trigger: 'change'
        }
      ]
    };
  }

  onSubmit() {
    this.$refs.addForm.validate(async valid => {
      if (valid) {
        this.loading = true;

        await this.addAction({ formData: this.form })
          .then(res => {
              this.loading = false;

              this.$message({
                showClose: true,
                message: this.$t('msg.addSuccess').toString(),
                type: 'success',
                duration: 3 * 1000
              })

              this.$refs.addForm.resetFields();
          })
          .catch(err => {
            this.loading = false;
          })
      } else {
        return false;
      }
    });
  }

  onReset() { return this.$refs.addForm.resetFields(); }

  created() { return this.initData(); }

  async initData() { return await this.formsourceAction() }
}
</script>
