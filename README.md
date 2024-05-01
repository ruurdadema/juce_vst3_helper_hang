# Minimal reproducible example

## Summary

This example shows a problem when juce::Timer is used as part of a singleton instance. When juce::Timer is used in this way, juce_vst3_helper.exe will hang when the plugin is closed.

To reproduce, build the project and see it never finish.

When the build hangs it's possible to attach a debugger to the juce_vst3_helper.exe process. When paused you'll get the stack trace below:

```
<unknown> 0x00007ffb2c36f3e4
<unknown> 0x00007ffb29bc421e
juce::Thread::sleep(int) juce_Threads_windows.cpp:208
juce::Thread::waitForThreadToExit(int) juce_Thread.cpp:227
juce::Thread::stopThread(int) juce_Thread.cpp:247
juce::Timer::TimerThread::~TimerThread() juce_Timer.cpp:40
juce::Timer::TimerThread::`scalar deleting destructor'(unsigned int) 0x00007ffa9ca9c008
std::_Ref_count::_Destroy() memory:1205
std::_Ref_count_base::_Decref() memory:1178
std::_Ptr_base::_Decref() memory:1403
std::shared_ptr::~shared_ptr<…>() memory:1687
juce::SharedResourcePointer::~SharedResourcePointer<…>() juce_SharedResourcePointer.h:102
juce::Timer::~Timer() juce_Timer.cpp:294
SingletonTimer::~SingletonTimer() PluginProcessor.cpp:206
`SingletonTimer::getInstance'::`2'::`dynamic atexit destructor for 'instance''() 0x00007ffa9d286f92
<unknown> 0x00007fface4c2c21
<unknown> 0x00007fface4c23f5
<unknown> 0x00007fface4c2547
<unknown> 0x00007fface4c2e34
__scrt_dllmain_uninitialize_c() 0x00007ffa9d1d2139
dllmain_crt_process_detach(const bool) 0x00007ffa9d1d35fc
dllmain_crt_dispatch(HINSTANCE__ *const,const unsigned long,void *const) 0x00007ffa9d1d33d7
dllmain_dispatch(HINSTANCE__ *const,const unsigned long,void *const) 0x00007ffa9d1d376e
_DllMainCRTStartup(HINSTANCE__ *const,const unsigned long,void *const) 0x00007ffa9d1d38e1
<unknown> 0x00007ffb2c2f869f
<unknown> 0x00007ffb2c3210a6
<unknown> 0x00007ffb2c320c9d
<unknown> 0x00007ffb2b9e7fab
<unknown> 0x00007fface4c1eda
<unknown> 0x00007fface4c1e85
<unknown> 0x00007fface4c21f6
__scrt_common_main_seh() 0x00007ff60b797f17
__scrt_common_main() 0x00007ff60b797dbe
wmainCRTStartup(void *) 0x00007ff60b7980ee
<unknown> 0x00007ffb2b9e257d
<unknown> 0x00007ffb2c32aa48
```

The way this is reproduced is by creating a juce::Timer as part of a singleton object. See `SingletonTimer` in `PluginProcessor.h`. The singleton instance is requested in the constructor of `PluginProcessor`.


## Environment:

Windows 11
Visual Studio 17 2022

https://forum.juce.com/t/timer-always-ensure-the-timer-thread-is-started-is-causing-juce-vst3-helper-exe-to-hang-indefinitely/61155
