# TinaX.Core FAQ

<br>

### 如何延迟执行任务（计划任务）

在旧版本TinaX中，延迟执行任务由TinaX的`TimeMachine`驱动,如等待5秒后执行逻辑：
``` csharp
using TinaX;
TimeMachine.Instance.AddTimerPlane(5,()=>{
    //xxxxxx
});
```
在TinaX 6.6之后该接口取消，改为建议使用`UniRx`方式：
``` csharp
using UniRx;
Observable.Timer(TimeSpan.FromSeconds(5))
    .Subscribe(_ =>
    {
        //xxxxx
    });
```