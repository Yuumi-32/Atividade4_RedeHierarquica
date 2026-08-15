# Atividade 4 — Simulação de ambiente hierárquico de rede local

**Disciplina:** Comutação de Redes Locais (TCN.0639)
**Curso:** Tecnologia em Redes de Computadores — IFRO, Campus Porto Velho Zona Norte
**Professor:** Jhordano Malacarne Bravim
**Aluno:** [seu nome completo]
**Ferramenta:** Cisco Packet Tracer [versão que você usou]

---

## 1. Diagrama da rede

![Diagrama da rede hierárquica](diagrama-rede.png)

Arquivo do simulador: [Atividade4_RedeHierarquica.pkt](Atividade4_RedeHierarquica.pkt)

---

## 2. Descrição da configuração escolhida

A rede foi montada em três camadas, seguindo o modelo hierárquico de redes locais.
Cada camada tem uma função distinta e um nível de redundância diferente.

### 2.1 Camada de núcleo

| Equipamento | Modelo | Função |
|---|---|---|
| SW-NUCLEO-01 | Switch-PT (modular) | Comutação de alta velocidade entre distribuição e roteador |
| SW-NUCLEO-02 | Switch-PT (modular) | Redundância do núcleo |

Os dois switches de núcleo estão ligados entre si por **4 cabos de cobre
GigabitEthernet em paralelo**. Essas quatro portas estão fisicamente preparadas
para receber, no futuro, uma agregação de link de **4 Gbps** (4 × 1 Gbps).
A agregação ainda não foi ativada, conforme o requisito 1 da atividade,
que pede apenas as ligações físicas.

### 2.2 Camada de distribuição

| Equipamento | Modelo | Função |
|---|---|---|
| SW-DIST-01 | Switch-PT (modular) | Agrega os switches de borda 01 e 02 |
| SW-DIST-02 | Switch-PT (modular) | Agrega os switches de borda 03 e 04 |

Cada switch de distribuição sobe para **os dois** switches de núcleo, usando
**interfaces de fibra óptica GigabitEthernet**. Cada enlace usa 2 fibras em
paralelo, fisicamente preparadas para uma agregação de link de **2 Gbps**
(2 × 1 Gbps). São 8 cabos de fibra no total.

Essa ligação cruzada garante que, se um switch de núcleo falhar, a distribuição
continua alcançando o restante da rede.

### 2.3 Camada de borda (acesso)

| Equipamento | Modelo | Dispositivos conectados |
|---|---|---|
| SW-BORDA-01 | Cisco 2960 | SRV-01, PC-01, PC-02 |
| SW-BORDA-02 | Cisco 2960 | PC-03, PC-04 |
| SW-BORDA-03 | Cisco 2960 | NOTE-01, NOTE-02 |
| SW-BORDA-04 | Cisco 2960 | NOTE-03, NOTE-04 |

Cada switch de borda tem **um único cabo** subindo para a distribuição, ligado
na porta GigabitEthernet0/1. Não há redundância nesta camada, conforme o
requisito 6 da atividade.

### 2.4 Roteador

O roteador **R-BORDA-WAN** (Cisco 1841) tem duas interfaces FastEthernet nativas:

- `FastEthernet0/0` → SW-NUCLEO-01
- `FastEthernet0/1` → SW-NUCLEO-02

Atende ao requisito 3: um roteador com duas interfaces FastEthernet
ligadas a dois switches diferentes.

### 2.5 Dispositivos finais

Nove dispositivos, todos conectados com fio:

- 4 computadores desktop (PC-01 a PC-04)
- 4 notebooks (NOTE-01 a NOTE-04)
- 1 servidor (SRV-01)

---

## 3. Justificativa das escolhas

### 3.1 Por que Switch-PT modular no núcleo e na distribuição

Os switches de modelo fixo do Packet Tracer (2950, 2960, 3560) possuem poucas
portas GigabitEthernet e não oferecem portas de fibra óptica. O Switch-PT é
modular, o que permitiu instalar exatamente a quantidade de portas de cobre
Gigabit e de fibra Gigabit exigida pelos requisitos 4 e 5.

### 3.2 Por que 4 cabos entre os switches de núcleo

Agregação de link soma a banda dos enlaces físicos. Como cada porta
GigabitEthernet entrega 1 Gbps, são necessários 4 enlaces para alcançar os
4 Gbps pedidos no requisito 4.

### 3.3 Por que 2 fibras entre núcleo e distribuição

Pela mesma lógica: 2 enlaces GigabitEthernet de fibra somam os 2 Gbps pedidos
no requisito 5.

### 3.4 Padrões 802.3 envolvidos

| Padrão | Onde aparece nesta rede |
|---|---|
| IEEE 802.3u | FastEthernet 100BASE-TX: roteador ↔ núcleo e dispositivos finais ↔ borda |
| IEEE 802.3ab | GigabitEthernet 1000BASE-T em cobre: núcleo ↔ núcleo e distribuição ↔ borda |
| IEEE 802.3z | GigabitEthernet 1000BASE-X em fibra: núcleo ↔ distribuição |
| IEEE 802.3ad / 802.1AX | Agregação de link (LACP), prevista para ativação futura |

Observação: a agregação de link foi padronizada originalmente como IEEE 802.3ad
e posteriormente transferida para o padrão IEEE 802.1AX.

### 3.5 Escalabilidade, hierarquia, redundância e disponibilidade

- **Hierarquia:** as três camadas estão separadas visualmente e por função.
- **Escalabilidade:** para crescer, basta acrescentar switches de borda na
  distribuição, sem alterar o núcleo.
- **Redundância:** o núcleo é duplicado e cada switch de distribuição tem dois
  caminhos independentes até o núcleo.
- **Disponibilidade:** a falha de um switch de núcleo, ou de um enlace de fibra,
  não derruba a rede.

---

## 4. Tipos de cabo utilizados

| Ligação | Cabo |
|---|---|
| Roteador ↔ switch de núcleo | Cobre direto (straight-through) |
| Switch ↔ switch (cobre) | Cobre crossover |
| Núcleo ↔ distribuição | Fibra óptica |
| Dispositivo final ↔ switch de borda | Cobre direto (straight-through) |

---

## 5. Endereçamento IP (item extra)

Todos os dispositivos finais foram configurados na mesma rede
**192.168.10.0/24**, máscara **255.255.255.0**.

| Dispositivo | IP |
|---|---|
| PC-01 | 192.168.10.11 |
| PC-02 | 192.168.10.12 |
| PC-03 | 192.168.10.13 |
| PC-04 | 192.168.10.14 |
| NOTE-01 | 192.168.10.21 |
| NOTE-02 | 192.168.10.22 |
| NOTE-03 | 192.168.10.23 |
| NOTE-04 | 192.168.10.24 |
| SRV-01 | 192.168.10.100 |

Como todos estão na mesma rede, a comunicação ocorre por comutação de camada 2,
sem necessidade de roteamento. O teste foi feito com o comando `ping` a partir
do PC-01 para os demais dispositivos.

Print do teste: ![Teste de ping](teste-ping.png)
