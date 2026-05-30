# CPU de 16 Bits – N2 (Logisim-evolution)

Projeto acadêmico de uma **CPU de 16 bits multi-ciclo** desenvolvida no **Logisim-evolution**, com suporte a memória, pilha e periféricos mapeados em hardware.

---

## Informações do Projeto

| Item | Detalhes |
|---|---|
| Disciplina | Arquitetura de Computadores |
| Avaliação | N2 |
| Ferramenta | Logisim-evolution 3.8.0 |
| Arquivo principal | `CPU.circ` |
| Professor | Vinícius S. Borges |

---

## Demonstração e Repositório

- 🎥 **Vídeo da apresentação:**  
  https://video.com
---

# Visão Geral

Este projeto implementa uma **CPU de 16 bits baseada em arquitetura multi-ciclo**

Esta CPU possui:

- Arquitetura **multi-ciclo**
- Unidade de controle sequencial
- Registradores dedicados
- Controle explícito de micro-etapas
- Memória de programa e memória de dados
- Sistema de pilha
- Periféricos mapeados em memória
- Saída TTY
- Sistema de vídeo com display matricial

---

# Arquitetura da CPU

## Especificações Gerais

| Característica | Valor |
|---|---|
| Largura de dados | 16 bits |
| Largura de endereços | 16 bits |
| Espaço de endereçamento | 2¹⁶ posições |
| Modelo de execução | Multi-ciclo |
| Clock | Global (`CLK`) |
| Enable de clock | `clk_enb` |

---

# Registradores Principais

## `CONTADOR_DE_PROGRAMA` (PC)

Responsável por armazenar o endereço da próxima instrução a ser buscada na ROM.

---

## `REGISTRADOR_DE_INSTRUCAO` (IR)

Armazena a instrução atual durante as etapas de busca, decodificação e execução.

---

## `REGISTRADOR_DE_ENDERECO_DE_MEMORIA` (MAR)

Responsável pelos endereços utilizados nos acessos à RAM e periféricos mapeados.

---

## `ACUMULADOR` (AC)

Principal registrador utilizado pela ULA para operações aritméticas e lógicas.

---

## `REGISTRADOR_B` (RB)

Segundo operando utilizado pela ULA e também em operações de memória e pilha.

---

## `REGISTRADORES_Y_E_Y`

Registradores auxiliares utilizados em micro-operações intermediárias do datapath.

---

## `FLAGS`

Conjunto de flags da CPU, utilizado para:

- Zero (`Z`)
- Carry (`C`)
- Outros sinais condicionais

Essas flags são atualizadas pela ULA e utilizadas pela unidade de controle.

---

## `REGISTRADOR_TTY`

Registrador associado ao periférico TTY responsável pela saída textual/numérica.

---

## `REGISTER_SAIDA`

Registrador auxiliar utilizado para depuração e monitoramento interno.

---

# Unidade de Controle

A unidade de controle foi implementada de forma distribuída utilizando:

- Contador de ciclos (`CONTADOR_CICLO`)
- Lógica combinacional baseada em:
  - Opcode da instrução
  - Estado atual do contador de ciclos

Essa lógica gera os sinais responsáveis por:

- Escrita em registradores
- Controle da ULA
- Controle da RAM
- Manipulação de pilha
- Escrita em periféricos
- Controle de vídeo

## Exemplos de sinais de controle

### Registradores

- `*_LOAD`
- `*_EN`

### ULA

- `ADD`
- `SUB`
- `A_XOR_B`

### Memória

- `ADDRESS_WRITE`
- `ADDRESS_SELECT`
- `RAM_IN`

### Pilha

- `STACK_UP`
- `STACK_DOWN`
- `STACK_STORE`
- `STACK_DATA_OUT`

---

# Datapath

O circuito principal (`CIRCUITO`) organiza o fluxo de dados entre os componentes da CPU.

## Fluxo principal

### PC → ROM → IR

1. O PC fornece o endereço da instrução.
2. A ROM retorna a palavra de 16 bits.
3. A instrução é armazenada no IR.

---

### IR → Unidade de Controle

A instrução armazenada é decodificada pela unidade de controle, que define:

- Operações da ULA
- Escrita/leitura de memória
- Manipulação de pilha
- Atualização de registradores

---

### AC / RB / ULA

Os dados trafegam entre:

- `ACUMULADOR`
- `REGISTRADOR_B`
- Registradores auxiliares
- ULA

A saída da ULA pode:

- Atualizar registradores
- Ser escrita em memória
- Ser enviada ao TTY
- Atualizar o vídeo

---

### MAR / RAM / Periféricos

O MAR controla os acessos de leitura e escrita em:

- RAM
- Pilha
- Buffer de vídeo
- Periféricos mapeados

---

# ULA (Unidade Lógica e Aritmética)

A ULA está encapsulada no subcircuito `ULA` e opera com largura de 16 bits.

## Operações implementadas

### Aritméticas

- `ADD`
- `SUB`

### Lógicas

- `A_XOR_B`

---

## Flags geradas

A ULA atualiza sinais utilizados pela unidade de controle, como:

- Zero
- Carry

Essas flags permitem a implementação de desvios condicionais e decisões de fluxo.

---

# Execução Multi-Ciclo

O controle de execução é realizado pelo subcircuito `CONTADOR_CICLO`.

Cada instrução é dividida em múltiplas etapas.

## Fluxo de execução

### 1. Fetch

- Leitura da instrução na ROM
- Carregamento no IR

---

### 2. Decode

- Interpretação do opcode
- Preparação dos sinais de controle

---

### 3. Execute

Execução efetiva da instrução:

- Operações da ULA
- Acesso à memória
- Manipulação de pilha
- Escrita em periféricos

---

### 4. Write-back

- Atualização de registradores
- Atualização do PC
- Reinicialização do contador de ciclos

---

# Memória e Periféricos

# ROM (Memória de Programa)

| Propriedade | Valor |
|---|---|
| Tipo | ROM |
| Endereçamento | 16 bits |
| Dados | 16 bits |

O programa é armazenado diretamente no componente ROM do Logisim.

## Alterando o programa

1. Clique com o botão direito na ROM
2. Selecione `Edit Contents...`
3. Edite os valores desejados
4. Salve as alterações

---

# RAM (Memória de Dados)

Utilizada para:

- Variáveis
- Pilha
- Dados intermediários
- Regiões mapeadas

Controlada principalmente pelo registrador MAR.

---

# Pilha

A pilha utiliza sinais específicos para manipulação:

| Sinal | Função |
|---|---|
| `STACK_UP` | Push |
| `STACK_DOWN` | Pop |
| `STACK_STORE` | Escrita |
| `STACK_DATA_OUT` | Leitura |

A implementação utiliza:

- Ponteiro de pilha
- Área reservada da RAM

---

# TTY

O periférico TTY permite saída textual/numérica através do registrador:

- `REGISTRADOR_TTY`

Usado principalmente para depuração e exibição de resultados.

---

# Sistema de Vídeo

O sistema gráfico é composto pelos seguintes subcircuitos:

| Subcircuito | Função |
|---|---|
| `PLACA_DE_VIDEO` | Controle de escrita de vídeo |
| `BUFFEFR_VIDEO` | Framebuffer |
| `DISPLAY` | Matriz 8×8 |

A CPU pode escrever diretamente no buffer de vídeo para alterar os pixels exibidos.

---

# Como Executar

## Requisitos

- Logisim-evolution 3.8.0 ou superior

---

## Executando o projeto

### 1. Abrir o circuito

- Abra o Logisim-evolution
- Carregue o arquivo `CPU16BIT.circ`
- Selecione o circuito principal `FINAL_BUILD`

---

### 2. Configurar a RAM

- Na RAM principal (parte superior), clique com o botão direito e selecione a opção `Carregar imagem...`
- Selecione o arquivo `FINAL_ASSEMBLY_OS_WITH_TETRIS_RAM_FILE`

---

### 3. Configurar as ROMs

- Na parte inferior do circuito principal, clique com o botão direito na primeira ROM e clique em `Carregar imagem...`
- Selecione o arquivo `OPCODE_ROM_MICROCODE_1`
- Clique com o botão direito na segunda ROM e clique em `Carregar imagem...`
- Selecione o arquivo `OPCODE_ROM_MICROCODE_2`
- Clique com o botão direito na terceira ROM e clique em `Carregar imagem...`
- Selecione o arquivo `OPCODE_ROM_MICROCODE_3`

---

### 3. Inicializar o sistema

- Pressione `CTRL + K` para habilitar o pulso no simulador
- Na parte superior do simulador, clique me `Simular` > `Frequência de pulso` e escolha `2.0 kHz` (valor sugerido para melhor experiência)
- Na parte inferior direita do circuito principal, habilite o `clock` no botão `clk_enb`

---

### 4. Iniciar simulação

- Logo abaixo do `TTY` há um `teclado` para digitar os comandos

---

# Equipe

| Integrante | RA |
|---|---|
| Emanuel Carneiro da Silva | 082230018 |
| João Vitor Maciel Nai | 082230004 |
| José Gabriel de Morais Souza | 082230001 |

---

# Referências

- 🎥 Vídeo de inspiração:  
  https://www.youtube.com/watch?v=GRM1Oc3erew

---