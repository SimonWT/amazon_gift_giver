<template>
  <section class="container">
    <div class="main">
      <el-row
        :type="$mq !== 'sm' ? 'flex' : ''"
        justify="center"
        :gutter="$mq !== 'sm' ? 100 : 0"
        style="width: 100%;"
      >
        <el-col
          :span="$mq !== 'sm' ? 12 : 24"
          style="
            justify-content: center;
            display: flex;
            align-items: center;
            flex-direction: column;
          "
        >
          <div class="main__text">
            <h1>LET US KNOW WHAT YOU THINK!</h1>
            <h2>
              Please tell us you experience with our Products and get a FREE
              Gift
            </h2>
            <h3>
              NO Shipping Charges, NO Hidden Fees, NO Credit Card Required!
            </h3>
          </div>
        </el-col>
        <el-col :span="$mq !== 'sm' ? 12 : 24">
          <div class="form">
            <h4>Claim your FREE Gift!</h4>
            <h5 style="font-weight: 200;">
              Fill out the form to receive a FREE product.
            </h5>
            <el-form
              ref="ruleForm"
              :model="form"
              label-width="120px"
              label-position="top"
              :rules="rules"
              style="width: 100%; margin-top: 30px;"
            >
              <el-form-item prop="name">
                <el-input v-model="fullname" placeholder="Fullname"></el-input>
              </el-form-item>
              <el-form-item prop="email">
                <el-input
                  v-model="email"
                  placeholder="Email Address"
                ></el-input>
              </el-form-item>
              <el-form-item>
                <el-button type="success" style="width: 100%;" @click="onSubmit"
                  >GET A GIFT</el-button
                >
                <p class="mt-3">
                  * Conditions Apply: Limited to one free gift of each product
                  per Amazon account and household.
                </p>
              </el-form-item>
            </el-form>
          </div>
        </el-col>
      </el-row>
    </div>
  </section>
</template>

<script>
export default {
  name: 'Main',
  data() {
    const validateEmail = (rule, value, callback) => {
      const re = /^(([^<>()[\].,;:\s@"]+(\.[^<>()[\].,;:\s@"]+)*)|(".+"))@(([^<>()[\].,;:\s@"]+\.)+[^<>()[\].,;:\s@"]{2,})$/i
      if (!value.match(re)) {
        callback(new Error('Email is wrong formated'))
      }
      callback()
    }
    return {
      form: {
        name: '',
        email: '',
      },
      rules: {
        name: [
          {
            required: true,
            message: 'Please input your Fullname!',
            trigger: 'change',
          },
        ],
        email: [
          {
            required: true,
            message: 'Please input your email!',
            trigger: 'change',
          },
          { validator: validateEmail, trigger: 'change' },
        ],
      },
    }
  },
  computed: {
    fullname: {
      get() {
        return this.$store.state.inputs.fullname
      },
      set(newValue) {
        this.form.name = newValue
        this.$store.commit('setInput', { field: 'fullname', data: newValue })
      },
    },
    email: {
      get() {
        return this.$store.state.inputs.email
      },
      set(newValue) {
        this.form.email = newValue
        this.$store.commit('setInput', { field: 'email', data: newValue })
      },
    },
  },
  methods: {
    onSubmit() {
      this.$refs.ruleForm.validate((valid) => {
        if (valid) {
          const push = this.$fireDb.ref(`/Feed`).push({
            name: this.fullname,
            email: this.email,
            last_filled_step: 0,
            time: new Date().getTime(),
          })
          this.$store.commit('setInput', { field: 'feedKey', data: push.key })
          this.$nuxt.$emit('setProgress', 31)
          this.$emit('nextStep', this.form)
        } else {
          console.log('error submit!!')
          return false
        }
      })
    },
  },
}
</script>

<style>
.main {
  margin: 0 !important;
}

.main__text {
  color: white !important;
}

.main__text h2 {
  line-height: 1.2;
  font-size: 30px;
  font-weight: 500;
  color: white;
}

.main__text h3 {
  line-height: 1.5;
  padding-top: 20px;
  font-size: 24px;
  font-weight: 300;
}

.form {
  /* margin-left: 5%; */
  background-color: rgba(15, 15, 15, 0.45);
  align-items: center;
  text-align: center;
  justify-content: center;
  flex-direction: column;
  display: flex;
  border-radius: 5px;
  padding-top: 40px;
  padding-left: 40px;
  padding-bottom: 10px;
  border-top-width: 2;
  border-right-width: 2;
  border-bottom-width: 2;
  border-left-width: 2;
  border-top-color: rgba(0, 0, 0, 0.1);
  border-right-color: rgba(0, 0, 0, 0.1);
  border-bottom-color: rgba(0, 0, 0, 0.1);
  border-left-color: rgba(0, 0, 0, 0.1);
  border-top-style: solid;
  border-right-style: solid;
  border-bottom-style: solid;
  border-left-style: solid;
  width: 33vw;
  max-width: 400px;
  padding-right: 40px;
  color: white;
}

.form p {
  line-height: 1rem;
}

@media (min-width: 320px) and (max-width: 760px) {
  .main__text {
    margin: 30px 0;
  }

  .main__text h3 {
    font-size: 18px;
  }

  .main__text h2 {
    font-size: 20px !important;
  }

  .form {
    margin-right: 0;
    width: 100%;
  }
}
</style>
