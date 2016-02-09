$inputXML = @"
<Window x:Class="PowerShellGUI.MainWindow"
        xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
        xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
        xmlns:local="clr-namespace:PowerShellGUI"
        mc:Ignorable="d"
        Title="PowerShell Active Directory Tool" Height="636.886" Width="583.094">
    <Grid>
        <Image x:Name="image" HorizontalAlignment="Left" Height="71" Margin="10,10,0,0" VerticalAlignment="Top" Width="100" Source="\\prhs5\groups\Information Systems\Read Only\Images\activedirectory.png"/>
        <TextBlock x:Name="textBlock" HorizontalAlignment="Left" Margin="115,29,0,0" TextWrapping="Wrap" Text="Use this tool to quickly get info on user accounts as well as reset passwords and unlock accounts." VerticalAlignment="Top"/>
        <Button x:Name="Ok_button" Content="Get User Info" HorizontalAlignment="Left" Margin="285,102,0,0" VerticalAlignment="Top" Height="25" Width="79"/>
        <TextBox x:Name="username_textBox" HorizontalAlignment="Left" Height="25" Margin="147,102,0,0" TextWrapping="Wrap" VerticalAlignment="Top" Width="120"/>
        <Label x:Name="username_label" Content="Enter username" HorizontalAlignment="Left" Margin="38,102,0,0" VerticalAlignment="Top"/>
        <Button x:Name="close_button" Content="Close" HorizontalAlignment="Left" Margin="10,576,0,0" VerticalAlignment="Top" Width="75"/>
        <TextBox x:Name="results" HorizontalAlignment="Left" Height="356" Margin="65,204,0,0" TextWrapping="Wrap" VerticalAlignment="Top" Width="448"/>
        <Button x:Name="UnlockButton" Content="Unlock" HorizontalAlignment="Left" Margin="377,103,0,0" VerticalAlignment="Top" Width="75" RenderTransformOrigin="0.08,0.106" Height="25"/>
        <Label x:Name="PasswordLabel" Content="New password" HorizontalAlignment="Left" Margin="38,165,0,0" VerticalAlignment="Top"/>
        <TextBox x:Name="PasswordTextBox" HorizontalAlignment="Left" Height="25" Margin="147,165,0,0" TextWrapping="Wrap" VerticalAlignment="Top" Width="120"/>
        <Button x:Name="NewPasswordButton" Content="Set Password" HorizontalAlignment="Left" Margin="285,165,0,0" VerticalAlignment="Top" Height="25" Width="79"/>
        <Label x:Name="NameLabel" Content="Enter name" HorizontalAlignment="Left" Margin="40,132,0,0" VerticalAlignment="Top"/>
        <TextBox x:Name="NametextBox" HorizontalAlignment="Left" Height="25" Margin="147,133,0,0" TextWrapping="Wrap" VerticalAlignment="Top" Width="120"/>
        <Button x:Name="NameEnterbutton" Content="Get Username" HorizontalAlignment="Left" Margin="285,133,0,0" VerticalAlignment="Top" RenderTransformOrigin="0.177,-0.067" Height="25" Width="79"/>
    </Grid>
</Window>




"@       
 
$inputXML = $inputXML -replace 'mc:Ignorable="d"','' -replace "x:N",'N'  -replace '^<Win.*', '<Window'
 
 
[void][System.Reflection.Assembly]::LoadWithPartialName('presentationframework')
[xml]$XAML = $inputXML
#Read XAML
 
    $reader=(New-Object System.Xml.XmlNodeReader $xaml) 
  try{$Form=[Windows.Markup.XamlReader]::Load( $reader )}
catch{Write-Host "Unable to load Windows.Markup.XamlReader. Double-check syntax and ensure .net is installed."}
 
#===========================================================================
# Load XAML Objects In PowerShell
#===========================================================================
 
$xaml.SelectNodes("//*[@Name]") | %{Set-Variable -Name "WPF$($_.Name)" -Value $Form.FindName($_.Name)}
 
Function Get-FormVariables{
if ($global:ReadmeDisplay -ne $true){Write-host "If you need to reference this display again, run Get-FormVariables" -ForegroundColor Yellow;$global:ReadmeDisplay=$true}
write-host "Found the following interactable elements from our form" -ForegroundColor Cyan
get-variable WPF*
}
 
Get-FormVariables
 
#===========================================================================
# Actually make the objects work
#===========================================================================

	#TODO: Place custom script here
$WPFOK_button.Add_Click({
	Function userinfo
	{
		Param (
			[Parameter(Mandatory = $True, ValuefromPipeline = $true)]
			[string]$user
		)
		Get-ADUser $user -Properties * | format-list Name, SamAccountName, PasswordExpired, PasswordLastSet, LastBadPasswordAttempt, LockedOut, LastLogondate
	}
	
	$user=$WPFusername_textbox.Text
	$WPFresults.Text = userinfo $user | Out-String
})
$WPFclose_button.Add_Click({$form.Close()})
     

$WPFUnlockButton.Add_Click({Function unlockuser
	{
		Param (
			[Parameter(Mandatory = $True, ValuefromPipeline = $true)]
			[string]$user
		)
		Unlock-ADAccount -Identity $user
	}
	$user = $WPFusername_textBox.Text
	$WPFresults = unlockuser $user | Out-String
})

$WPFNewPasswordButton.Add_Click({
	
		
		$SecurePassword = ConvertTo-SecureString $WPFPasswordTextBox.Text -AsPlainText -Force
		$user=$WPFusername_textBox.Text
		Set-ADAccountPassword -identity $user -NewPassword $SecurePassword
	})

$WPFNameEnterbutton.Add_Click({
    Function usernameinfo
	{
		Param (
			[Parameter(Mandatory = $True, ValuefromPipeline = $true)]
			[string]$name
		)
        
		$filt='*'+$name+'*'
        $a=@{Expression={$_.Name};Label="Name";width=50},
            @{Expression={$_.samaccountname};Label="User Name";width=50}
        Get-ADUser -Filter {name -like $filt} | Format-Table $a
	}
    $name=$WPFNametextBox.Text
	$WPFresults.Text = usernameinfo $name | Out-String -Width 100
})
	
#Sample entry of how to add data to a field
 
#$vmpicklistView.items.Add([pscustomobject]@{'VMName'=($_).Name;Status=$_.Status;Other="Yes"})
 
#===========================================================================
# Shows the form
#===========================================================================
write-host "To show the form, run the following" -ForegroundColor Cyan
$Form.ShowDialog() | out-null
