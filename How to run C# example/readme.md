### EEG Tutorial 1: EEG Data Reading using C# and DotNetEmotivSDK.dll

This example demonstrates how to extract live EEG data using the EmoEngineTM in c#. Data is read from the headset and sent to an output file for later analysis.

**Project set-up:**

EEG_Logger is a c# console project. To enable access to the EmoEngineTM via managed code, it is necessary to reference the .NET dll - DotNetEmotivSDK.dll, which can be found in the installation folder of the EDK. This dll exposes the EmoEngineTM class, which is explained further in the ‘API Reference for dot NET’ help file.

**Listing 1: Initialization**

```c
EmoEngine engine;

```

We assign our engine reference to the global EmoEngineTM via the ‘Instance’ property. This instance property is used to access all EmoEngine functions:

```c
engine = EmoEngine.Instance;
```

We add a new event handler to the UserAdded event, which our application will process. This is necessary to

```c
engine.UserAdded += new EmoEngine.UserAddedEventHandler(engine_UserAdded_Event);
```

We commence access to the engine by a call to Connect():

```c
engine.Connect();
```

And finally we create a pre-defined column header for the CSV file:

```c
 // create a header for our output file
```

**Listing 2: Main loop**

```c
static void Main(string[] args)
```

The main loop of the application simply creates the logging class, loops 10 times and exits. The total amount of data collected is approximately 1 second’s worth.

**Listing 3: User connection**

```c
void engine_UserAdded_Event(object sender, EmoEngineEventArgs e) {
```

To enable data collection via EmoEngine, it is necessary to ensure a valid user has connected. Once connected (and the event thrown), data acquisition is enabled with a call registering the connected user as follows:

```c
engine.DataAcquisitionEnable((uint)userID, true);
```

The EmoEngine maintains an internal buffer of sampled data. To adjust the size of this data, make a call as follows:

```c
engine.EE_DataSetBufferSizeInSec(1);
```

At this point, you are ready to collect data.

**Listing 4: Data collection**

```c
// Handle any waiting events
```

We first process any waiting engine events with a call as follows. This is necessary to ensure the user has connected and our user-setup code is called.
engine.ProcessEvents();
```

We confirm that a valid user has connected by checking the ID (Note - the first user to connect has an ID of 0)

```c
 if ((int)userID == -1)
```

We then request our data as shown. This call will return all available EEG data currently held in the EmoEngine buffers. For each data channel, we receive an array of type double, whose length is the number of samples collected since the last time GetData was called. Note that if GetData is called too infrequently the buffers may overflow.

```c
	Dictionary<EdkDll.EE_DataChannel_t, double[]> data = engine.GetData((uint)userID);
```

We can easily establish the number of samples captured as follows:

```c
int _bufferSize = data[EdkDll.EE_DataChannel_t.TIMESTAMP].Length;
```

To access the data (in this case, we output directly to a file), it is simply a matter of looping over all data held in the ‘data’ structure as follows:

```c
for (int i = 0; i < _bufferSize; i++)
```