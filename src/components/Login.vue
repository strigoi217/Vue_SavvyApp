<template>
    <div id="login">
        <section>
            <div class="col1">
                <h1>Vuegram</h1>
                <p>Welcome to the <a href="https://savvyapps.com/" target="_blank">Savvy Apps</a> sample social media web app powered by Vue.js and Firebase.
                    Build this project by checking out The Definitive Guide to Getting Started with Vue.js</p>
            </div>
            <div class="col2" :class="{ 'signup-form': !showLoginForm }">
                 <form v-if="showLoginForm" @submit.prevent>
                    <h1>Welcome Back</h1>

                    <label for="email1">Email</label>
                    <input v-model.trim="loginForm.email" type="text" placeholder="you@email.com" id="email1" />

                    <label for="password1">Password</label>
                    <input v-model.trim="loginForm.password" type="password" placeholder="******" id="password1" />

                    <button class="button">Log In</button>

                    <div class="extras">
                        <a>Forgot Password</a>
                        <a>Create an Account</a>
                    </div>
                </form>

                <form v-else @submit.prevent>
                    <h1>Get Started</h1>

                    <label for="name">Name</label>
                    <input v-model.trim="signupForm.name" type="text" placeholder="Savvy Apps" id="name" />

                    <label for="title">Title</label>
                    <input v-model.trim="signupForm.title" type="text" placeholder="Company" id="title" />

                    <label for="email2">Email</label>
                    <input v-model.trim="signupForm.email" type="text" placeholder="you@email.com" id="email2" />

                    <label for="password2">Password</label>
                    <input v-model.trim="signupForm.password" type="password" placeholder="min 6 characters" id="password2" />

                    <label for="password2">Confrim</label>
                    <input v-model.trim="signupForm.confirm" type="confirm" placeholder="min 6 characters" id="confirm" />

                    <button @click="signup" class="button">Sign Up</button>

                    <div class="extras">
                        <a>Back to Log In</a>
                    </div>
                </form>
            </div>
        </section>
    </div>
</template>

<script>
    const fb = require('../firebaseconfig.js')

    export default {
        data() {
            return {
                loginForm: {
                    email: '',
                    password: ''
                },
                signupForm: {
                    name: '',
                    title: '',
                    email: '',
                    password: '',
                    confirm: ''
                }
            }
        },
        methods: {
            toggleForm() {
                this.showLoginForm = !this.showLoginForm
            },
            togglePasswordReset() {
                if (this.showForgotPassword) {
                    this.showLoginForm = true
                    this.showForgotPassword = false
                    this.passwordResetSuccess = false
                } else {
                    this.showLoginForm = false
                    this.showForgotPassword = true
                }
            },
            login() {
                fb.auth.signInWithEmailAndPassword(this.loginForm.email, this.loginForm.password).then(user => {
                    this.$store.commit('setCurrentUser', user)
                    this.$store.dispatch('fetchUserProfile')
                    this.$router.push('/dashboard')
                }).catch(err => {
                    console.log(err)
                })
            },
            signup() {
                fb.auth.createUserWithEmailAndPassword(this.signupForm.email, this.signupForm.password).then(user => {
                    this.$store.commit('setCurrentUser', user)

                    // create user obj
                    fb.usersCollection.doc(user.uid).set({
                        name: this.signupForm.name,
                        title: this.signupForm.title,
                        password: this.signupForm.password
                    }).then(() => {
                        this.$store.dispatch('fetchUserProfile')
                        this.$router.push('/dashboard')
                    }).catch(err => {
                        console.log(err)
                    })
                }).catch(err => {
                    console.log(err)
                })
            }
        }
        
    }
</script>
