Implementing Login on Android with FirebaseUI
=========================

This repository contains the final code for the Login Codelab.

Firebase Authentication (Kotlin)

Firebase Auth UI Library
    implementation 'com.firebaseui:firebase-ui-auth:5.0.0'


From Fragment/Activity
    viewModel.authenticationState.observe(viewLifecycleOwner, Observer { authenticationState ->
        when (authenticationState) {
            LoginViewModel.AuthenticationState.AUTHENTICATED -> {
                binding.authButton.text = getString(R.string.logout_button_text)
                binding.authButton.setOnClickListener {
                    AuthUI.getInstance().signOut(requireContext())
                }
    
                binding.welcomeText.text = getFactWithPersonalization(factToDisplay)
            }
            else -> {
                binding.authButton.text = getString(R.string.login_button_text)
                binding.authButton.setOnClickListener { launchSignInFlow() }
                binding.welcomeText.text = factToDisplay
            }
        }
    })


- launghSignInFlow()
    private fun launchSignInFlow() {
        // Give users the option to sign in / register with their email or Google account.
        // If users choose to register with their email,
        // they will need to create a password as well.
        val providers = arrayListOf(
            AuthUI.IdpConfig.EmailBuilder().build(), AuthUI.IdpConfig.GoogleBuilder().build()
    
            // This is where you can provide more ways for users to register and
            // sign in.
        )
    
        // Create and launch the sign-in intent.
        // We listen to the response of this activity with the
        // SIGN_IN_REQUEST_CODE.
        startActivityForResult(
            AuthUI.getInstance()
                .createSignInIntentBuilder()
                .setAvailableProviders(providers)
                .build(),
            SIGN_IN_REQUEST_CODE
        )
    }


- onActivityResult
    override fun onActivityResult(requestCode: Int, resultCode: Int, data: Intent?) {
        super.onActivityResult(requestCode, resultCode, data)
        if (requestCode == SIGN_IN_REQUEST_CODE) {
            val response = IdpResponse.fromResultIntent(data)
            if (resultCode == Activity.RESULT_OK) {
                // User successfully signed in.
                Log.i(TAG, "Successfully signed in user ${FirebaseAuth.getInstance().currentUser?.displayName}!")
            } else {
                // Sign in failed. If response is null, the user canceled the
                // sign-in flow using the back button. Otherwise, check
                // the error code and handle the error.
                Log.i(TAG, "Sign in unsuccessful ${response?.error?.errorCode}")
            }
        }
    }


- From LoginViewModel
    val authenticationState = FirebaseUserLiveData().map { user ->
        if (user != null) {
            AuthenticationState.AUTHENTICATED
        } else {
            AuthenticationState.UNAUTHENTICATED
        }
    }


- FirebaseUserLiveData
    class FirebaseUserLiveData : LiveData<FirebaseUser?>() {
        private val firebaseAuth = FirebaseAuth.getInstance()
    
        // TODO set the value of this FireUserLiveData object by hooking it up to equal the value of the
        //  current FirebaseUser. You can utilize the FirebaseAuth.AuthStateListener callback to get
        //  updates on the current Firebase user logged into the app.
        private val authStateListener = FirebaseAuth.AuthStateListener { firebaseAuth ->
            // TODO Use the FirebaseAuth instance instantiated at the beginning of the class to get an
            //  entry point into the Firebase Authentication SDK the app is using.
            //  With an instance of the FirebaseAuth class, you can now query for the current user.
            value = firebaseAuth.currentUser
        }
    
        // When this object has an active observer, start observing the FirebaseAuth state to see if
        // there is currently a logged in user.
        override fun onActive() {
            firebaseAuth.addAuthStateListener(authStateListener)
        }
    
        // When this object no longer has an active observer, stop observing the FirebaseAuth state to
        // prevent memory leaks.
        override fun onInactive() {
            firebaseAuth.removeAuthStateListener(authStateListener)
        }
    }
Reference
- https://developer.android.com/codelabs/advanced-android-kotlin-training-login?hl=en&continue=https%3A%2F%2Fcodelabs.developers.google.com%2F%3Fhl%3Den#0


My Git Project
- https://github.com/thitlwin/FirebaseUIAuth.git


License
-------

Copyright 2019 Google, Inc.

Licensed to the Apache Software Foundation (ASF) under one or more contributor
license agreements.  See the NOTICE file distributed with this work for
additional information regarding copyright ownership.  The ASF licenses this
file to you under the Apache License, Version 2.0 (the "License"); you may not
use this file except in compliance with the License.  You may obtain a copy of
the License at

http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS, WITHOUT
WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.  See the
License for the specific language governing permissions and limitations under
the License.

