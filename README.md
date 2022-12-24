# CoroutineFlowPlayGround


### Difference between 'collect' and collect latest Flow Kotlin
When flow emits a new value then the action block for the previous value is cancelled.

##### ViewModel.kt
```kt 
private val _mutableSharedFlow = MutableSharedFlow<Int>(replay = 0)

    val sharedFlow: SharedFlow<Int> = _mutableSharedFlow
    fun updateFlow() {
        // TODO diff view model scope async and launch
        viewModelScope.launch() {
            _mutableSharedFlow.emit(1)
            delay(100)
            _mutableSharedFlow.emit(2)
        }
    }
  ```
 ##### Activity.kt 
 ```kt
       lifecycleScope.launch {
            viewModel.sharedFlow.collectLatest {
                Log.d("Collect_Latest", "first value$it")
                delay(2000)
                Log.d("Collect_Latest", "second value$it")
                delay(2000)
                Log.d("Collect_Latest ", "third  value$it")
            }
        }

        /* OUTPUT :
                 first  value1
                 first  value2
                 second value2
                 third  value2*/

        lifecycleScope.launch {
            viewModel.sharedFlow.collect {
                Log.d("Collect", "first value$it")
                delay(2000)
                Log.d("Collect ", "second value$it")
                delay(2000)
                Log.d("Collect ", "third  value$it")
            }
        }
        viewModel.updateFlow()
              /* OUTPUT :
                 first  value1
                 second value1
                 third  value1
                 first  value2
                 second value2
                 third  value2*/
                 
   ```
### Difference between Shared Flow and State Flow
StateFlow is a state-holder observable(observable holds state unlike Shared flow) flow that emits the current and new state updates to its collectors.
The current state value can also be read through its value property.
The main difference between a SharedFlow and a StateFlow is that a StateFlow takes a default value through the constructor and emits it immediately when someone starts collecting, while a SharedFlow takes no value and emits nothing by default.
##### ViewModel.kt
```kt
private val _mutableStateFlow = MutableStateFlow<Int>(0)

    val stateFlow: StateFlow<Int> = _mutableStateFlow

    private val _mutableSharedFlow = MutableSharedFlow<Int>(0)

    val sharedFlow: SharedFlow<Int> = _mutableSharedFlow
    fun updateFlow() {
        viewModelScope.launch() {
            _mutableSharedFlow.emit(1)
            _mutableStateFlow.emit(1)
            delay(100)
            _mutableSharedFlow.emit(2)
            _mutableStateFlow.emit(2)
        }
    }
   ```
##### Activity.kt   
```kt
    lifecycleScope.launch {
            viewModel.stateFlow.collect {
                Log.d("Collect", "State Flow value $it")
            }
        }
        lifecycleScope.launch {
            viewModel.sharedFlow.collect {
                Log.d("Collect", "Shared Flow value $it")
            }
        }
        Log.d("Collect", "${viewModel.stateFlow.value}")
        viewModel.updateFlow()
   ```
   
  
