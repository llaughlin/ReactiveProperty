# What is ReactiveProperty

ReactiveProperty provides MVVM and asynchronous support features under Reactive Extensions. Target framework is .NET Standard 2.0.

![Summary](./images/rpsummary.png)

Concept of ReactiveProperty is <b>Fun programing</b>.
You can write MVVM pattern program using ReactiveProperty very fun.

![UWP](./images/launch-uwp-app.gif)

Following code is tow way binding between ReactiveProperty and plane object property.

```csharp
class Model : INotifyPropertyChanged
{
    public event PropertyChangedEventHandler PropertyChanged;

    private string _name;
    public string Name
    {
        get => _name;
        set
        {
            _name = value;
            PropertyChanged?.Invoke(this, new PropertyChangedEventArgs(nameof(Name)));
        }
    }
}
class ViewModel
{
    private readonly Model _model = new Model();
    public ReactiveProperty<string> Name { get; }
    public ViewModel()
    {
        // TwoWay synchronize to ReactiveProperty and Model#Name property.
        Name = _model.ToReactivePropertyAsSynchronized(x => x.Name);
    }
}
```

ReactiveProperty is implemented the IObservable&lt;T&gt;. Yes! You can use LINQ.

```csharp
var name = new ReactiveProperty<string>();
name.Where(x => x.StartsWith("_")) // filter
    .Select(x => x.ToUpper()) // convert
    .Subscribe(x => { ... some action ... });
```

ReactiveProperty is created from IObservable&lt;T&gt;. 

```csharp
class ViewModel
{
    public ReactiveProperty<string> Input { get; }
    public ReactiveProperty<string> Output { get; }

    public ViewModel()
    {
        Input = new ReactiveProperty("");
        Output = Input
            .Delay(TimeSpan.FromSecond(1)) // Using a Rx method.
            .Select(x => x.ToUpper()) // Using a LINQ method.
            .ToReactiveProperty(); // Convert to ReactiveProperty
    }
}
```

This method chain is very cool.

And we provide ReactiveCommand class what implements ICommand and IObservable&lt;T&gt; interfaces. ReactiveCommand can create from an IObservable&lt;bool&gt;.
Following sample is creating a ReactiveCommand that is be able to execute when Input property is not empty.

```csharp
class ViewModel
{
    public ReactiveProperty<string> Input { get; }
    public ReactiveProperty<string> Output { get; }

    public ReactiveCommand ResetCommand { get; }

    public ViewModel()
    {
        Input = new ReactiveProperty("");
        // Same as above sample
        Output = Input
            .Delay(TimeSpan.FromSecond(1)) // Using a Rx method.
            .Select(x => x.ToUpper()) // Using a LINQ method.
            .ToReactiveProperty(); // Convert to ReactiveProperty
        
        ResetCommand = Input.Select(x => !string.IsNullOrWhitespace(x)) // Convert ReactiveProperty<string> to IObservable<bool>
            .ToReactiveCommand() // You can create ReactiveCommand from IObservable<bool> (When true value was published, then the command would be able to execute.)
            .WithSubscribe(() => Input.Value = ""); // This is a shortcut of ResetCommand.Subscribe(() => ...)
    }
}
```

Cool!! It is really declarative, really cleary.

## Let's start!

You can start the ReactiveProperty from following links.

- [Windows Presentation Foundation](getting-started/wpf.md)
- [Universal Windows Platform](getting-started/uwp.md)
- [Xamarin.Forms](getting-started/xf.md)

And learn to core features on following links.

- [ReactiveProperty](features/ReactiveProperty.md)
- [Commanding](features/Commanding.md)
- [Collections](features/Collections.md)
