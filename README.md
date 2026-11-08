# OS_Shutdown

Aplicativo de console para agendar o desligamento ou a reinicialização de um computador Windows. O programa solicita horas, minutos e segundos e usa o utilitário nativo `shutdown.exe` para criar ou cancelar o timer.

## Recursos

- Agendar o desligamento do computador.
- Agendar a reinicialização do computador.
- Cancelar um timer ativo.
- Consultar se o Windows já informa um timer ativo.
- Exibir uma contagem regressiva e o horário previsto para a ação.

## Requisitos

- Windows, pois o aplicativo depende de `shutdown.exe`.
- .NET 10 SDK para compilar e executar pelo código-fonte.

## Executar

Na pasta do repositório:

```powershell
dotnet run --project .\OS_Shutdown.csproj
```

No menu, escolha a operação e informe horas, minutos e segundos como números inteiros não negativos. Os campos podem exceder 59; o programa os converte para a apresentação da contagem regressiva. Uma duração total igual a zero retorna ao menu sem agendar uma ação.

Durante a contagem regressiva, pressione `C` para sair da tela e voltar ao menu. Isso **não cancela** a operação já agendada no Windows. Para cancelá-la, escolha a opção `3` no menu.

## Compilar

```powershell
dotnet restore .\OS_Shutdown.sln
dotnet build .\OS_Shutdown.sln --configuration Release
```

Para publicar um executável autocontido de arquivo único para Windows x64:

```powershell
dotnet publish .\OS_Shutdown.csproj `
  --configuration Release `
  --runtime win-x64 `
  --self-contained true `
  /p:PublishSingleFile=true `
  /p:PublishTrimmed=false `
  --output publish
```

O workflow `Build Release` em `.github/workflows/release.yml` pode ser iniciado manualmente no GitHub Actions. Ele publica `OS_Shutdown.exe` para `win-x64` e cria uma release com uma tag no formato `build-N`.

## Observações

- O timer é mantido pelo Windows, não pelo processo do aplicativo. Fechar a aplicação ou sair da tela da contagem regressiva não interrompe o timer.
- A opção de verificação não consulta um estado independente: ela tenta agendar um desligamento temporário para daqui a 24 horas e, se não detectar um timer ativo pelo código de saída, executa o cancelamento. Portanto, essa opção faz alterações temporárias no agendador do Windows.
- A aplicação interpreta códigos de saída específicos do `shutdown.exe` para detectar timer ativo ou inexistente. Outros erros de execução podem ser apresentados como se o agendamento ou cancelamento tivesse sido concluído; confira o estado do Windows se houver falha.
- O programa não possui interface gráfica nem parâmetros de linha de comando.