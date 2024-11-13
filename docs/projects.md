# Relatório de Implementação - Tutorial Criando Métricas em .NET

Este relatório documenta cada etapa do tutorial "Criando Métricas" em .NET, incluindo a implementação de métricas personalizadas e o monitoramento das mesmas com o `dotnet-counters`.

## 1. Configuração do Ambiente

### 1.1 Pré-requisitos
- **SDK do .NET Core**: Versão 6 ou posterior foi instalada para permitir o desenvolvimento do projeto.

### 1.2 Criação do Projeto
Com o SDK configurado, iniciamos um novo aplicativo de console e adicionamos o pacote `System.Diagnostics.DiagnosticSource` para instrumentação das métricas.

Comandos executados:
```bash
dotnet new console -o CriandoMetricasApp
cd CriandoMetricasApp
dotnet add package System.Diagnostics.DiagnosticSource --version 8.0.0

## 2. Implementação da Métrica Personalizada

### 2.1 Código Inicial - Contador de Chapéus Vendidos

Para iniciar o tutorial, criamos uma métrica do tipo `Counter<int>` no arquivo `Program.cs`. Esse contador rastreia o número de chapéus vendidos por segundo, simulando uma transação constante de 4 chapéus por segundo.

Código utilizado:

```csharp
using System;
using System.Diagnostics.Metrics;
using System.Threading;

class Program
{
    static Meter s_meter = new Meter("HatCo.Store");
    static Counter<int> s_hatsSold = s_meter.CreateCounter<int>("hatco.store.hats_sold");

    static void Main(string[] args)
    {
        Console.WriteLine("Press any key to exit");
        while(!Console.KeyAvailable)
        {
            Thread.Sleep(1000);
            s_hatsSold.Add(4);
        }
    }
}

```

### 2.2 Explicação do Código

- **`Meter`**: Define um grupo nomeado de instrumentos. Aqui, o `Meter` é chamado `HatCo.Store`, representando nossa "loja".
- **`Counter<int>`**: Criamos um contador chamado `hatco.store.hats_sold`, que registra o número de chapéus vendidos (4 chapéus por segundo neste exemplo).

## 3. Monitoramento da Métrica

Para monitorar a métrica personalizada, utilizamos a ferramenta `dotnet-counters`, que exibe as contagens em tempo real.

### 3.1 Executando o Código

Para iniciar o código, executamos:

```bash
dotnet run

```

### 3.2 Monitoramento com `dotnet-counters`

Em um segundo terminal, monitoramos o contador criado com o comando:

```bash
dotnet-counters monitor -n CriandoMetricasApp --counters HatCo.Store

```

### 3.3 Saída do Monitoramento

Abaixo, uma captura de tela da saída no console, que mostra o número de chapéus vendidos sendo atualizado constantemente:

```
Press p to pause, r to resume, q to quit.
    Status: Running

[HatCo.Store]
    hatco.store.hats_sold (Count)                  372

```

O contador `hatco.store.hats_sold` aumenta em 4 a cada segundo, conforme esperado.