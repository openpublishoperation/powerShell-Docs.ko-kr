# 모듈의 버전 범위 선언(1.* 등) 지원
**-MinimumVersion**과 함께 **-MaximumVersion**을 사용하여 사용자는 특정 범위 내의 모듈을 가져올 수 있습니다. 이 매개 변수는 **.***도 지원합니다. 다음 예제에서는 작동 방식을 보여 줍니다.

```PowerShell
Now, you can combine **-MinimumVersion** and **-MaximumVersion** to import module within specific range:

PS C:\> Import-Module psreadline -Verbose -MinimumVersion 1.0 -MaximumVersion 1.2.*

VERBOSE: Loading module from path 'C:\Program Files\WindowsPowerShell\Modules\psreadline\1.1\psreadline.psd1'.
VERBOSE: Importing cmdlet 'Get-PSReadlineKeyHandler'.
VERBOSE: Importing cmdlet 'Get-PSReadlineOption'.
VERBOSE: Importing cmdlet 'Remove-PSReadlineKeyHandler'.
VERBOSE: Importing cmdlet 'Set-PSReadlineKeyHandler'.
VERBOSE: Importing cmdlet 'Set-PSReadlineOption'.
VERBOSE: Importing function 'PSConsoleHostReadline'.
```


<!--HONumber=Jun16_HO4-->


