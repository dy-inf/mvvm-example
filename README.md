### Рассмотрим пример приложения для отображения списка задач.  Покажем, как ViewModel и View работают вместе, используя ViewModel для управления данными и логикой, а View для отображения информации.
## Model (Задача):

    public class TaskItem
    {
        public string Title { get; set; }
        public bool IsCompleted { get; set; }
    }

## ViewModel (Список задач):

    using System.Collections.ObjectModel;
    using System.ComponentModel;
    using System.Runtime.CompilerServices;
    
    public class TaskListViewModel : INotifyPropertyChanged
    {
        public event PropertyChangedEventHandler PropertyChanged;
    
        private ObservableCollection<TaskItem> _tasks;
        public ObservableCollection<TaskItem> Tasks
        {
            get => _tasks;
            set
            {
                _tasks = value;
                OnPropertyChanged();
            }
        }
    
        public TaskListViewModel()
        {
            Tasks = new ObservableCollection<TaskItem>()
            {
                new TaskItem { Title = "Купить молоко", IsCompleted = false },
                new TaskItem { Title = "Сходить в спортзал", IsCompleted = true },
                new TaskItem { Title = "Позвонить маме", IsCompleted = false }
            };
        }
    
        protected virtual void OnPropertyChanged([CallerMemberName] string propertyName = null)
        {
            PropertyChanged?.Invoke(this, new PropertyChangedEventArgs(propertyName));
        }
    
        // Можно добавить команды для добавления, удаления, изменения состояния задач
        // ...
    }

## View (Список задач в XAML):

    <Window x:Class="..."
            ...>
        <Window.DataContext>
            <local:TaskListViewModel/>
        </Window.DataContext>
        <ListBox ItemsSource="{Binding Tasks}">
            <ListBox.ItemTemplate>
                <DataTemplate>
                    <StackPanel Orientation="Horizontal">
                        <CheckBox IsChecked="{Binding IsCompleted}"/>
                        <TextBlock Text="{Binding Title}"/>
                    </StackPanel>
                </DataTemplate>
            </ListBox.ItemTemplate>
        </ListBox>
    </Window>

## Объяснение:
* Model (TaskItem):  Простая модель данных, представляющая одну задачу.

* ViewModel (TaskListViewModel):  Содержит коллекцию Tasks типа ObservableCollection<TaskItem>.  ObservableCollection автоматически уведомляет View об изменениях в коллекции (добавление, удаление, изменение элементов).  INotifyPropertyChanged используется для уведомления View об изменениях других свойств ViewModel.

* View (XAML):  ListBox отображает коллекцию Tasks из ViewModel.  DataTemplate определяет, как каждый элемент TaskItem будет отображаться (чекбокс и текст).  {Binding Tasks} связывает свойство Tasks из ViewModel с ItemsSource ListBox.  {Binding IsCompleted} и {Binding Title} связывают свойства IsCompleted и Title из TaskItem с IsChecked и Text элементов CheckBox и TextBlock соответственно.

В этом примере ViewModel отвечает за управление данными (задачами), а View — за отображение этих данных.  Изменение данных в ViewModel автоматически обновляет отображение в View благодаря привязке данных.  Это разделение обеспечивает чистоту кода, тестируемость и  поддерживаемость приложения.
