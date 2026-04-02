# 質問

この画像は、アプリのUIデモとして作成しました。
この画像のようなUIを、xamlで作ってください。
コードベースには、バインディングコンテキストとクリックコマンド等のイベントコマンドを書く予定です。(理由は私が書きなれているからです。)スタブだけ作ってください。

このViewの名前はMainMenuViewで、対応するVMはMainMenuVMです。コンストラクタでMainMenuVMを受け取って、バインディングコンテキストに設定し、privateフィールドに保存するところまでやってください。

命名規則は以下の通りです。このxaml中で登場する名前はすべてアッパーキャメルケースで作ってください。
GridやButtonなどは、(コントロールの種類3文字)(コントロールの役割)という名前にしてください。(例：BtnSetting)
データバインディングに設定するプロパティは、Bind_(プロパティ名)にしてください。(例：Bind_WorldName)
ボタンに登録するメソッドは、OnBtn(ボタン名)Clickedとしてください。(例：OnBtnSettingClicked)

右側の、 |画像|ワールド名・プロパティ| が縦に並んでいる部分は、このような同種のコンテンツがたくさんあって、スクロールバーで選べるようにしたいです。また、各コンテンツをクリックすると、設定したコマンドが起動するようにしたいです。

# 回答

ご提示いただいたデモ画面のレイアウトに基づき、WPFや.NET MAUIなどのXAMLフレームワークで汎用的に利用できる構成で作成しました。

命名規則（`(コントロール3文字)(役割)`、`Bind_`、`OnBtn...Clicked`）を厳守し、ViewModelの実装もスタブとして含めています。

### MainMenuView.xaml
画面全体をGridで分割し、左側にメイン操作系、右側に設定・リスト・ログを配置しています。

```xml
<ContentPage xmlns="http://schemas.microsoft.com/dotnet/2021/maui"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="YourApp.Views.MainMenuView"
             Title="MainMenuView">

    <Grid ColumnDefinitions="*, *" Padding="20" ColumnSpacing="20">
        
        <VerticalStackLayout Grid.Column="0" Spacing="15">
            <Label x:Name="LblMainTitle" 
                   Text="メイン画面" 
                   HorizontalOptions="Center" 
                   FontSize="24" 
                   BackgroundColor="#1A5276" 
                   TextColor="White" 
                   Padding="10"/>

            <Entry x:Name="EntWorldName" 
                   Placeholder="ワールド名" 
                   Text="{Binding Bind_WorldName}"/>

            <BoxView x:Name="BoxWorldDisplay" 
                     HeightRequest="300" 
                     Color="#1A5276" 
                     CornerRadius="0"/>

            <Button x:Name="BtnLoadWorld" 
                    Text="ワールド読み込み" 
                    WidthRequest="200"
                    HorizontalOptions="Center"
                    Clicked="OnBtnLoadWorldClicked"
                    Command="{Binding Bind_BtnLoadWorldCommand}"/>
        </VerticalStackLayout>

        <VerticalStackLayout Grid.Column="1" Spacing="15">
            <Button x:Name="BtnSetting" 
                    Text="設定" 
                    WidthRequest="150"
                    HorizontalOptions="End"
                    Clicked="OnBtnSettingClicked"
                    Command="{Binding Bind_BtnSettingCommand}"/>

            <Border StrokeThickness="1" HeightRequest="250">
                <CollectionView x:Name="ColWorldList"
                                ItemsSource="{Binding Bind_WorldList}"
                                SelectionMode="Single"
                                SelectionChanged="OnColWorldListSelectionChanged">
                    <CollectionView.ItemTemplate>
                        <DataTemplate>
                            <Grid ColumnDefinitions="60, *" Padding="5">
                                <Image Source="{Binding Bind_Thumbnail}" 
                                       Aspect="AspectFill" 
                                       WidthRequest="50" 
                                       HeightRequest="50"/>
                                <Label Grid.Column="1" 
                                       Text="{Binding Bind_DisplayName}" 
                                       VerticalTextAlignment="Center" 
                                       Padding="10,0"/>
                            </Grid>
                        </DataTemplate>
                    </CollectionView.ItemTemplate>
                </CollectionView>
            </Border>

            <Button x:Name="BtnAddWorld" 
                    Text="ワールド追加" 
                    WidthRequest="200"
                    HorizontalOptions="Center"
                    Clicked="OnBtnAddWorldClicked"
                    Command="{Binding Bind_BtnAddWorldCommand}"/>

            <Editor x:Name="EdiLogDisplay" 
                    Text="{Binding Bind_LogText}" 
                    HeightRequest="150" 
                    IsReadOnly="True" 
                    Placeholder="ログ"/>
        </VerticalStackLayout>

    </Grid>
</ContentPage>
```

---

### MainMenuView.xaml.cs
コンストラクタでViewModelを受け取り、プライベートフィールドへの保持と`BindingContext`への設定を行っています。

```csharp
using YourApp.ViewModels;

namespace YourApp.Views;

public partial class MainMenuView : ContentPage
{
    private MainMenuVM _viewModel;

    public MainMenuView(MainMenuVM viewModel)
    {
        InitializeComponent();
        _viewModel = viewModel;
        this.BindingContext = _viewModel;
    }

    private void OnBtnLoadWorldClicked(object sender, EventArgs e)
    {
        // 必要に応じてView固有の処理を記述
    }

    private void OnBtnSettingClicked(object sender, EventArgs e)
    {
    }

    private void OnBtnAddWorldClicked(object sender, EventArgs e)
    {
    }

    private void OnColWorldListSelectionChanged(object sender, SelectionChangedEventArgs e)
    {
        // リスト項目が選択された際のコマンド実行など
        if (_viewModel.Bind_BtnItemSelectCommand.CanExecute(e.CurrentSelection.FirstOrDefault()))
        {
            _viewModel.Bind_BtnItemSelectCommand.Execute(e.CurrentSelection.FirstOrDefault());
        }
    }
}
```

---

### MainMenuVM.cs
プロパティとコマンドのスタブ構成です。リスト項目のための内部クラスも含めています。

```csharp
using System.Collections.ObjectModel;
using System.Windows.Input;

namespace YourApp.ViewModels;

public class MainMenuVM : BindableObject
{
    // プロパティ
    private string _bindWorldName;
    public string Bind_WorldName
    {
        get => _bindWorldName;
        set { _bindWorldName = value; OnPropertyChanged(); }
    }

    private string _bindLogText;
    public string Bind_LogText
    {
        get => _bindLogText;
        set { _bindLogText = value; OnPropertyChanged(); }
    }

    public ObservableCollection<WorldItem> Bind_WorldList { get; set; } = new();

    // コマンド
    public ICommand Bind_BtnLoadWorldCommand { get; }
    public ICommand Bind_BtnSettingCommand { get; }
    public ICommand Bind_BtnAddWorldCommand { get; }
    public ICommand Bind_BtnItemSelectCommand { get; }

    public MainMenuVM()
    {
        // コマンドの初期化（スタブ）
        Bind_BtnLoadWorldCommand = new Command(() => OnBtnLoadWorldExecuted());
        Bind_BtnSettingCommand = new Command(() => OnBtnSettingExecuted());
        Bind_BtnAddWorldCommand = new Command(() => OnBtnAddWorldExecuted());
        Bind_BtnItemSelectCommand = new Command<WorldItem>((item) => OnBtnItemSelectExecuted(item));

        // デモ用データ
        Bind_WorldList.Add(new WorldItem { Bind_DisplayName = "ワールド名・プロパティ" });
    }

    private void OnBtnLoadWorldExecuted() { }
    private void OnBtnSettingExecuted() { }
    private void OnBtnAddWorldExecuted() { }
    private void OnBtnItemSelectExecuted(WorldItem item) { }
}

public class WorldItem
{
    public string Bind_DisplayName { get; set; }
    public string Bind_Thumbnail { get; set; } // 画像パスやURL
}
```

右側のリスト部分は `CollectionView`（または `ListView`）を使用することで、要素が増えた際に自動的にスクロールバーが表示されるようになっています。各要素のクリックイベントは、`SelectionChanged`経由でViewModelのコマンドを叩く構成にしています。