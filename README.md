# ia_grafos_buscar_Diogotakafaz
**Nome completo:** Diogo takafaz dos Santos

**Matricula:** 2250382

**Turma** : Engenharia da Computação

**Link do GitHub**import heapq
import heapq

class MapaEntrega:
    def __init__(self):
        self.grafo = {}

    def adicionar_trecho(self, ponto1, ponto2, minutos):
        if ponto1 not in self.grafo:
            self.grafo[ponto1] = []
        if ponto2 not in self.grafo:
            self.grafo[ponto2] = []

        self.grafo[ponto1].append((ponto2, minutos))
        self.grafo[ponto2].append((ponto1, minutos))

    def encontrar_menor_caminho(self, partida):
        tempos = {local: float('inf') for local in self.grafo}
        anteriores = {local: None for local in self.grafo}
        tempos[partida] = 0

        fila = [(0, partida)]

        while fila:
            tempo_atual, local = heapq.heappop(fila)

            for proximo, duracao in self.grafo[local]:
                novo_tempo = tempo_atual + duracao
                if novo_tempo < tempos[proximo]:
                    tempos[proximo] = novo_tempo
                    anteriores[proximo] = local
                    heapq.heappush(fila, (novo_tempo, proximo))

        return tempos, anteriores

def montar_rota(anteriores, destino):
    rota = []
    atual = destino

    while atual is not None:
        rota.append(atual)
        atual = anteriores[atual]

    return rota[::-1]

def testar_entregas():
    mapa = MapaEntrega()

    mapa.adicionar_trecho('Centro_Distribuicao', 'Mercado', 4)
    mapa.adicionar_trecho('Centro_Distribuicao', 'Escola', 6)
    mapa.adicionar_trecho('Mercado', 'Shopping', 3)
    mapa.adicionar_trecho('Escola', 'Shopping', 2)
    mapa.adicionar_trecho('Escola', 'Parque', 5)
    mapa.adicionar_trecho('Shopping', 'Cliente_Final', 4)
    mapa.adicionar_trecho('Parque', 'Cliente_Final', 3)

    inicio = 'Centro_Distribuicao'
    destino = 'Cliente_Final'

    tempos, anteriores = mapa.encontrar_menor_caminho(inicio)
    melhor_rota = montar_rota(anteriores, destino)

    print(f"Tempo total: {tempos[destino]} minutos")
    print(f"Melhor rota: {' → '.join(melhor_rota)}")

    print("\nTempos para todos os pontos:")
    for local, tempo in tempos.items():
        print(f"{local}: {tempo} minutos")

testar_entregas()Tempo total: 11 minutos
Melhor rota: Centro_Distribuicao → Mercado → Shopping → Cliente_Final

Tempos para todos os pontos:
Centro_Distribuicao: 0 minutos
Mercado: 4 minutos
Escola: 6 minutos
Shopping: 7 minutos
Parque: 11 minutos
Cliente_Final: 11 minutos**Parte B — A-Star (Busca Informada com Heurística Admissível)**

> Agora, considere um robô autônomo que deve se deslocar por um labirinto 2D, evitando obstáculos e chegando ao destino no menor tempo possível.
Diferente do Dijkstra, o robô pode usar uma heurística (como a distância ao alvo) para priorizar rotas promissoras, economizando tempo de busca.

**Atividades**

1. Gere um grid 20x20 com ~15% de obstáculos aleatórios.

2. Implemente a função heuristic(a, b) (distância Manhattan).

3. Desenvolva o algoritmo a_star(grid, start, goal, h) e teste-o.

**Explique (Questões Discursivas):**

1. A* vs Dijkstra: qual expande menos nós? A vs Dijkstra:* O A* é mais inteligente porque usa uma "dica" para ir direto ao alvo.

2. Por que a heurística Manhattan é admissível nesse caso?
Heurística Manhattan: É a menor distância possível em grid, então nunca engana.

💡 Cenário real: O A* é amplamente usado em robôs aspiradores, drones e jogos. Seu desafio é aplicar o mesmo raciocínio.import random

def distancia_reta(a, b):
    return abs(a[0]-b[0]) + abs(a[1]-b[1])

def busca_a_estrela(mapa, comeco, objetivo):
    fronteira = [(0, comeco)]
    origem = {}
    custo_real = {comeco: 0}

    while fronteira:
        fronteira.sort()
        custo_f, atual = fronteira.pop(0)

        if atual == objetivo:
            trajeto = []
            while atual in origem:
                trajeto.append(atual)
                atual = origem[atual]
            return trajeto[::-1]

        x, y = atualfor mov_x, mov_y in [(0,1),(1,0),(0,-1),(-1,0)]:
            novo_x, novo_y = x + mov_x, y + mov_y

            if 0 <= novo_x < 10 and 0 <= novo_y < 10 and mapa[novo_y][novo_x] == 0:
                novo_custo = custo_real[atual] + 1
                nova_pos = (novo_x, novo_y)

                if nova_pos not in custo_real or novo_custo < custo_real[nova_pos]:
                    custo_real[nova_pos] = novo_custo
                    prioridade = novo_custo + distancia_reta(nova_pos, objetivo)
                    fronteira.append((prioridade, nova_pos))
                    origem[nova_pos] = atual

    return None

labirinto = [
    [0, 0, 0, 0, 0, 0, 0, 0, 0, 0],
    [0, 1, 1, 0, 0, 1, 0, 1, 0, 0],
    [0, 0, 0, 0, 1, 1, 0, 0, 0, 0],
    [0, 1, 0, 1, 0, 0, 0, 1, 1, 0],
    [0, 0, 0, 0, 0, 1, 0, 0, 0, 0],
    [0, 1, 1, 1, 0, 0, 1, 1, 0, 0],
    [0, 0, 0, 1, 0, 1, 0, 0, 0, 0],
    [0, 1, 0, 0, 0, 1, 0, 1, 1, 0],
    [0, 1, 1, 0, 1, 0, 0, 0, 0, 0],
    [0, 0, 0, 0, 0, 0, 0, 1, 0, 0]
]

print("- LABIRINTO-")
print("R = início, F = fim, X = obstáculo, * = caminho")
print()

print("Labirinto original:")
for y in range(10):
    linha = ""
    for x in range(10):
        if (x,y) == (0,0):
            linha += "R "
        elif (x,y) == (9,9):
            linha += "F "
        elif labirinto[y][x] == 1:
            linha += "X "
        else:
            linha += ". "for mov_x, mov_y in [(0,1),(1,0),(0,-1),(-1,0)]:
            novo_x, novo_y = x + mov_x, y + mov_y

            if 0 <= novo_x < 10 and 0 <= novo_y < 10 and mapa[novo_y][novo_x] == 0:
                novo_custo = custo_real[atual] + 1
                nova_pos = (novo_x, novo_y)

                if nova_pos not in custo_real or novo_custo < custo_real[nova_pos]:
                    custo_real[nova_pos] = novo_custo
                    prioridade = novo_custo + distancia_reta(nova_pos, objetivo)
                    fronteira.append((prioridade, nova_pos))
                    origem[nova_pos] = atual

    return None

labirinto = [
    [0, 0, 0, 0, 0, 0, 0, 0, 0, 0],
    [0, 1, 1, 0, 0, 1, 0, 1, 0, 0],
    [0, 0, 0, 0, 1, 1, 0, 0, 0, 0],
    [0, 1, 0, 1, 0, 0, 0, 1, 1, 0],
    [0, 0, 0, 0, 0, 1, 0, 0, 0, 0],
    [0, 1, 1, 1, 0, 0, 1, 1, 0, 0],
    [0, 0, 0, 1, 0, 1, 0, 0, 0, 0],
    [0, 1, 0, 0, 0, 1, 0, 1, 1, 0],
    [0, 1, 1, 0, 1, 0, 0, 0, 0, 0],
    [0, 0, 0, 0, 0, 0, 0, 1, 0, 0]
]

print("- LABIRINTO-")
print("R = início, F = fim, X = obstáculo, * = caminho")
print()

print("Labirinto original:")
for y in range(10):
    linha = ""
    for x in range(10):
        if (x,y) == (0,0):
            linha += "R "
        elif (x,y) == (9,9):
            linha += "F "
        elif labirinto[y][x] == 1:
            linha += "X "
        else:
            linha += ". "
    print(linha)

caminho_encontrado = busca_a_estrela(labirinto, (0,0), (9,9))

if caminho_encontrado:
    print(f"\nCaminho encontrado! {len(caminho_encontrado)} movimentos")

    print("\nLabirinto com caminho:")
    for y in range(10):
        linha = ""
        for x in range(10):
            if (x,y) == (0,0):
                linha += "R "
            elif (x,y) == (9,9):
                linha += "F "
            elif (x,y) in caminho_encontrado:
                linha += "* "
            elif labirinto[y][x] == 1:
                linha += "X "
            else:
                linha += ". "
        print(linha)

    print(f"\nCoordenadas: {caminho_encontrado}")
else:
    print("\nNão há caminho possível!")..
. X X . X . . . . * 
. . . . . . . X . F 

Coordenadas: [(1, 0), (2, 0), (3, 0), (4, 0), (5, 0), (6, 0), (6, 1), (6, 2), (6, 3), (6, 4), (7, 4), (8, 4), (8, 5), (8, 6), (9, 6), (9, 7), (9, 8), (9, 9)]*Parte C — Árvores Binárias e Percursos (DFS em-ordem, pré-ordem e pós-ordem)**

> Você está desenvolvendo um sistema de recomendação que organiza produtos em uma árvore binária de busca (BST), conforme o preço. Cada nó é um produto e a travessia da árvore pode ser usada para: 1. Ordenar produtos (em-ordem); 2. Clonar a estrutura (pré-ordem); 3. Calcular totais ou liberar memória (pós-ordem);
class Item:
    def __init__(self, codigo):
        self.codigo = codigo
        self.esquerda = None
        self.direita = None

class SistemaClassificacao:
    def __init__(self):
        self.raiz = None

    def incluir(self, codigo):
        if self.raiz is None:
            self.raiz = Item(codigo)
        else:
            self._incluir(self.raiz, codigo)

    def _incluir(self, item, codigo):
        if codigo < item.codigo:
            if item.esquerda:
                self._incluir(item.esquerda, codigo)
            else:
                item.esquerda = Item(codigo)
        else:
            if item.direita:
                self._incluir(item.direita, codigo)
            else:
                item.direita = Item(codigo)

    def listar_ordenado(self, item=None):
        if item is None:
            item = self.raiz

        lista = []
        if item:
            if item.esquerda:
                lista += self.listar_ordenado(item.esquerda)
            lista.append(item.codigo)
            if item.direita:
                lista += self.listar_ordenado(item.direita)
        return lista

    def listar_pre(self, item=None):
        if item is None:
            item = self.raiz

        lista = []
        if item:
            lista.append(item.codigo)
            if item.esquerda:
                lista += self.listar_pre(item.esquerda)
            if item.direita:
                lista += self.listar_pre(item.direita)
        return lista

    def listar_pos(self, item=None):
        if item is None:
            item = self.raiz

        lista = []
        if item:
            if item.esquerda:
                lista += self.listar_pos(item.esquerda)
            if item.direita:
                lista += self.listar_pos(item.direita)
            lista.append(item.codigo)
        return lista

sistema = SistemaClassificacao()
codigos = [105, 67, 145, 42, 89, 122, 178, 55, 99]

for cod in codigos:
    sistema.incluir(cod)

print("Códigos em ordem:", sistema.listar_ordenado())
print("Códigos pré-ordem:", sistema.listar_pre())
print("Códigos pós-ordem:", sistema.listar_pos())
**Atividades**

1. Crie uma BST com os valores: [50, 30, 70, 20, 40, 60, 80, 35, 45].

Implemente os percursos:

1. in_order(root)

2. pre_order(root)

3. post_order(root)
class Item:
    def __init__(self, codigo):
        self.codigo = codigo
        self.esquerda = None
        self.direita = None

class SistemaClassificacao:
    def __init__(self):
        self.raiz = None

    def incluir(self, codigo):
        if self.raiz is None:
            self.raiz = Item(codigo)
        else:
            self._incluir(self.raiz, codigo)

    def _incluir(self, item, codigo):
        if codigo < item.codigo:
            if item.esquerda:
                self._incluir(item.esquerda, codigo)
            else:
                item.esquerda = Item(codigo)
        else:
            if item.direita:
                self._incluir(item.direita, codigo)
            else:
                item.direita = Item(codigo)

    def listar_ordenado(self, item=None):
        if item is None:
            item = self.raiz

        lista = []
        if item:
            if item.esquerda:
                lista += self.listar_ordenado(item.esquerda)
            lista.append(item.codigo)
            if item.direita:
                lista += self.listar_ordenado(item.direita)
        return lista

    def listar_pre(self, item=None):
        if item is None:
            item = self.raiz

        lista = []
        if item:
            lista.append(item.codigo)
            if item.esquerda:
                lista += self.listar_pre(item.esquerda)
            if item.direita:
                lista += self.listar_pre(item.direita)
        return lista

    def listar_pos(self, item=None):
        if item is None:
            item = self.raiz

        lista = []
        if item:
            if item.esquerda:
                lista += self.listar_pos(item.esquerda)
            if item.direita:
                lista += self.listar_pos(item.direita)
            lista.append(item.codigo)
        return lista

sistema = SistemaClassificacao()
codigos = [105, 67, 145, 42, 89, 122, 178, 55, 99]

for cod in codigos:
    sistema.incluir(cod) lista = []
        if item:
            lista.append(item.codigo)
            if item.esquerda:
                lista += self.listar_pre(item.esquerda)
            if item.direita:
                lista += self.listar_pre(item.direita)
        return lista

    def listar_pos(self, item=None):
        if item is None:
            item = self.raiz

        lista = []
        if item:
            if item.esquerda:
                lista += self.listar_pos(item.esquerda)
            if item.direita:
                lista += self.listar_pos(item.direita)
            lista.append(item.codigo)
        return lista
Parte D — Reflexões (Respostas Curtas)**

Responda de forma argumentativa (5–10 linhas cada):
*
1. Quando não é vantajoso usar A*, mesmo tendo uma heurística?
Quando o custo de calcular a heurística é muito alto ou quando o ambiente muda rápido.

2. Diferencie corretude e otimalidade nos algoritmos estudados.
Corretude = sempre acha resposta. Otimalidade = acha a melhor resposta.

3. Dê um exemplo do mundo real onde cada tipo de percurso (em, pré, pós) é essencial.
Em-ordem = lista de produtos por preço. Pré-ordem = cópia de estrutura. Pós-ordem = cálculo de totais.

4. Como heurísticas inconsistentes podem afetar o resultado do A*?
Pode fazer o algoritmo demorar mais ou achar caminho não ótimo.

💬 Sugestão: use exemplos de mapas, jogos, sistemas de busca ou árvores sintáticas.Parte D — Reflexões (Respostas Curtas)**

Responda de forma argumentativa (5–10 linhas cada):
*
1. Quando não é vantajoso usar A*, mesmo tendo uma heurística?
Quando o custo de calcular a heurística é muito alto ou quando o ambiente muda rápido.

2. Diferencie corretude e otimalidade nos algoritmos estudados.
Corretude = sempre acha resposta. Otimalidade = acha a melhor resposta.

3. Dê um exemplo do mundo real onde cada tipo de percurso (em, pré, pós) é essencial.
Em-ordem = lista de produtos por preço. Pré-ordem = cópia de estrutura. Pós-ordem = cálculo de totais.

4. Como heurísticas inconsistentes podem afetar o resultado do A*?
Pode fazer o algoritmo demorar mais ou achar caminho não ótimo.

💬 Sugestão: use exemplos de mapas, jogos, sistemas de busca ou árvores sintáticas.Parte D — Reflexões (Respostas Curtas)**

Responda de forma argumentativa (5–10 linhas cada):
*
1. Quando não é vantajoso usar A*, mesmo tendo uma heurística?
Quando o custo de calcular a heurística é muito alto ou quando o ambiente muda rápido.

2. Diferencie corretude e otimalidade nos algoritmos estudados.
Corretude = sempre acha resposta. Otimalidade = acha a melhor resposta.

3. Dê um exemplo do mundo real onde cada tipo de percurso (em, pré, pós) é essencial.
Em-ordem = lista de produtos por preço. Pré-ordem = cópia de estrutura. Pós-ordem = cálculo de totais.

4. Como heurísticas inconsistentes podem afetar o resultado do A*?
Pode fazer o algoritmo demorar mais ou achar caminho não ótimo.

💬 Sugestão: use exemplos de mapas, jogos, sistemas de busca ou árvores sintáticas.**Entrega**

Crie um repositório público chamado ia-grafos-seu-nome.

Envie para o repositório o arquivo ia_grafos_buscas.ipynb.

Submeta o link do repositório no ambiente da disciplina **Portal Digital Fametro.**.*Integridade Acadêmica**

1. O trabalho é individual.
2. Discussões conceituais são permitidas, mas o código deve ser inteiramente autoral.
3. Verificações de similaridade serão aplicadas a todas as submissões.
4. Busque e estude os algoritmos através de pesquisas na internet, livros, slides da disciplina.

print("Códigos em ordem:", sistema.listar_ordenado())
print("Códigos pré-ordem:", sistema.listar_pre())
print("Códigos pós-ordem:", sistema.listar_pos())
Teste se as saídas correspondem às travessias esperadas.

**Explique (Questões Discursivas):**

1. Em que situação cada tipo de percurso é mais indicado?

Em-ordem: Catálogos ordenados, relatórios classificados

Pré-ordem: Backup de pastas, clone de estruturas

Pós-ordem: Cálculo de totais, liberação de memória
    print(linha)

caminho_encontrado = busca_a_estrela(labirinto, (0,0), (9,9))

if caminho_encontrado:
    print(f"\nCaminho encontrado! {len(caminho_encontrado)} movimentos")

    print("\nLabirinto com caminho:")
    for y in range(10):
        linha = ""
        for x in range(10):
            if (x,y) == (0,0):
                linha += "R "
            elif (x,y) == (9,9):
                linha += "F "
            elif (x,y) in caminho_encontrado:
                linha += "* "
            elif labirinto[y][x] == 1:
                linha += "X "
            else:
                linha += ". "
        print(linha)

    print(f"\nCoordenadas: {caminho_encontrado}")
else:
    print("\nNão há caminho possível!")
