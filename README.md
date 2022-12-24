# CoroutineFlowPlayGround


### About The Project

Difference between collect and collect latest Flow Kotlin

 ```kt
  lifecycleScope.launch {
            viewModel.stateFlow.collect {
                Log.d("Collect", "State Flow value $it")
            }
        }
   ```
