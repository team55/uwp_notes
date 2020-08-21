
to list item
<ItemsControl x:Name="VaccinationsList" ItemsSource="{x:Bind ViewModel.Vactinations}" DataContext="{x:Bind ViewModel}">

<Button Style="{StaticResource IconButton}"
        Command="{Binding ElementName=VaccinationsList, Path=DataContext.DeleteVaccineCommand}"
        CommandParameter="{Binding}" 
        >
    <SymbolIcon Symbol="Delete" />
</Button>



