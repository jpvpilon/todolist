# TodoList

App Android de lista de tarefas, desenvolvido em **Kotlin** com **Jetpack Compose**. Permite criar, editar, concluir e excluir tarefas, com persistência local via **Room**.

## Funcionalidades

- Listar tarefas cadastradas (mais recentes primeiro)
- Criar nova tarefa (título e descrição)
- Editar tarefa existente
- Marcar/desmarcar tarefa como concluída (checkbox, com texto riscado)
- Excluir tarefa
- Tela de estado vazio quando não há tarefas

## Tecnologias

- Kotlin
- Jetpack Compose (Material 3)
- Room (persistência local em SQLite)
- Navigation Compose
- Arquitetura MVVM (`ViewModel` + `StateFlow`) com camada de `Repository`
- Coroutines / Kotlin Flow

## Arquitetura

```
data/         Entidade Tarefa, DAO e banco (Room)
repository/   TarefaRepository — abstrai o acesso ao DAO
viewmodel/    TarefaViewModel — expõe estado via StateFlow
navigation/   Navegação entre a lista e o formulário
ui/           Telas Compose (ListaTarefasScreen, FormularioTarefaScreen)
```

## Modelo de dados

O app tem uma única entidade Room, sem relacionamentos entre tabelas (sem chaves estrangeiras) — a persistência é um CRUD simples sobre uma tabela.

**`Tarefa`** (tabela `tarefas`)

| Campo | Tipo | Descrição |
|---|---|---|
| `id` | `Int` | Chave primária, autogerada |
| `titulo` | `String` | Título da tarefa (obrigatório) |
| `descricao` | `String` | Descrição opcional |
| `concluida` | `Boolean` | Status de conclusão (padrão `false`) |
| `dataCriacao` | `Long` | Timestamp de criação, usado para ordenar a lista (mais recentes primeiro) |

### Relação entre as camadas

Como só existe uma entidade, a "relação" relevante do projeto é o fluxo de dados entre as camadas da arquitetura MVVM, cada uma dependendo apenas da anterior:

```
Tarefa (entidade)
   ↓ mapeada pelo
TarefaDao          → operações SQL (listar, inserir, atualizar, deletar) sobre a tabela `tarefas`
   ↓ exposto pelo
TarefaDatabase      → instância singleton do Room que fornece o TarefaDao
   ↓ usado pelo
TarefaRepository    → abstrai o Dao e expõe as tarefas como Flow<List<Tarefa>>
   ↓ consumido pelo
TarefaViewModel     → converte o Flow em StateFlow e expõe inserir/atualizar/deletar
   ↓ observado pelas
Telas Compose (UI)  → ListaTarefasScreen (lista) e FormularioTarefaScreen (criar/editar),
                      conectadas via AppNavigation
```

Ou seja: a `UI` nunca acessa o `Dao` ou o banco diretamente — toda comunicação passa pelo `ViewModel`, que por sua vez delega ao `Repository`, mantendo as camadas desacopladas.

## Requisitos

- Android Studio (versão recente)
- SDK mínimo: Android 7.0 (API 24)
- SDK alvo: API 36

## Como rodar

```bash
git clone <url-do-repositorio>
```

Abra o projeto no Android Studio e execute em um emulador ou dispositivo físico, ou via linha de comando:

```bash
./gradlew installDebug
```

## Evidencias

<p align="center">
  <img src="app/docs/evidencias/Captura%20de%20tela%202026-08-26%20233622.png" width="30%" />
  <img src="app/docs/evidencias/Captura%20de%20tela%202026-08-26%20233708.png" width="30%" />
  <img src="app/docs/evidencias/Captura%20de%20tela%202026-08-26%20233800.png" width="30%" />
</p>
<p align="center">
  <img src="app/docs/evidencias/Captura%20de%20tela%202026-08-26%20233828.png" width="30%" />
  <img src="app/docs/evidencias/Captura%20de%20tela%202026-08-26%20233851.png" width="30%" />
  <img src="app/docs/evidencias/Captura%20de%20tela%202026-08-26%20234143.png" width="30%" />
</p>
