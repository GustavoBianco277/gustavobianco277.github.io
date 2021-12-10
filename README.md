from random import randint
import pygame


def detectCollisions(x1, y1, w1, h1, x2, y2, w2, h2):
    if x2 + w2 >= x1 >= x2 and y2 + h2 >= y1 >= y2:

        return True

    elif x2 + w2 >= x1 + w1 >= x2 and y2 + h2 >= y1 >= y2:

        return True

    elif x2 + w2 >= x1 >= x2 and y2 + h2 >= y1 + h1 >= y2:

        return True

    elif x2 + w2 >= x1 + w1 >= x2 and y2 + h2 >= y1 + h1 >= y2:

        return True

    else:

        return False


class Sprite:

    def __init__(self, x, y, width, height):

        self.x = x

        self.y = y

        self.width = width

        self.height = height
    # define se o retangulo esta colidindo
    def render(self, collision):

        if collision:

            pygame.draw.rect(janela, (255, 0, 0), (self.x, self.y, self.width, self.height))

        else:

            pygame.draw.rect(janela, (255, 255, 255), (self.x, self.y, self.width, self.height))


pygame.init()
Fecho = False
x = 210  # max 500 min 210
y = 200
posX = 250
posY = -300
px = 450
py = 1500
timer2 = timer = tempo = acelera = 0
cod = tempoTr = 5
velocidade = 10
velocidadeCarros = 5
fundo = pygame.image.load('lib/sprites/Rua.png')
carro = pygame.image.load('lib/sprites/carro.png')
carroAzul = pygame.image.load('lib/sprites/carroAzul.png')
carroBranco = pygame.image.load('lib/sprites/carroBranco.png')
font = pygame.font.SysFont('arial black', 30)
texto = font.render(f'Tempo: {tempo} S', True, (255, 255, 255))
PosTexto = texto.get_rect()
PosTexto.center = (80, 50)

# cria o texto Game Over
fontOver = pygame.font.SysFont('arial black', 80)
GameOver = fontOver.render('Você Perdeu !', True, (255, 0, 0))
PosGameOver = GameOver.get_rect()
PosGameOver.center = (400, 300)

# cria o texto de transiçao
fontTrans = pygame.font.SysFont('arial black', 20)
TextTransicao = fontTrans.render(f'Saia da transicao em: {tempoTr}', True, (255, 0, 0))
PosTransicao = TextTransicao.get_rect()
PosTransicao.center = (400, 150)

# Cria uma janela 800 x 600
janela = pygame.display.set_mode((800, 600))

# Define o nome da janela
pygame.display.set_caption("Jogo")
JanelaAberta = True

while JanelaAberta:
    MeuCarro = Sprite(x + 7, y + 5, 80, 190)
    CarroAzul = Sprite(posX + 5, posY + 15, 80, 190)
    CarroBranco = Sprite(px + 10, py, 80, 190)
    AreaTransicao = Sprite(374, 0, 50, 300)
    
    # Faz um delay de 50 milisegundos
    pygame.time.delay(50)
    colisao = detectCollisions(MeuCarro.x, MeuCarro.y, MeuCarro.width, MeuCarro.height, CarroAzul.x, CarroAzul.y, CarroAzul.width, CarroAzul.height)
    colisao2 = detectCollisions(CarroBranco.x, CarroBranco.y, CarroBranco.width, CarroBranco.height, MeuCarro.x, MeuCarro.y, MeuCarro.width, MeuCarro.height)
    colisao3 = detectCollisions(AreaTransicao.x, AreaTransicao.y, AreaTransicao.width, AreaTransicao.height, MeuCarro.x, MeuCarro.y, MeuCarro.width, MeuCarro.height)
    
    AreaTransicao.render(colisao3)
    MeuCarro.render(colisao2)  # meu carro colidir com o carro azul
    CarroAzul.render(colisao)
    MeuCarro.render(colisao)  # meu carro colidir com o carro Branco
    CarroBranco.render(colisao2)
    
    # Analiza os eventos
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            JanelaAberta = False
            
    # Define o movimento para cada tecla
    if not colisao and not colisao2 and not Fecho:
        comandos = pygame.key.get_pressed()
        # Movimenta o carro
        if comandos[pygame.K_LEFT] and x >= 210:
            x -= velocidade
        if comandos[pygame.K_RIGHT] and x <= 500:
            x += velocidade
        # Spawna os carros
        if posY >= 1000:
            posY = randint(-1200, -210)
        if py <= -215:
            py = randint(1001, 2000)
        if -207 <= posY <= 1007:
            py = randint(1007, 2000)
        if 1000 >= py >= 0:
            posY = randint(-1200, -300)
        # Define o timer
        if timer <= 20:
            timer += 1
        else:
            tempo += 1
            texto = font.render(f'Tempo: {tempo} S', True, (255, 255, 255))
            timer = 0
            acelera += 1
            if acelera == 10:
                velocidadeCarros += 5
                if velocidade < 40:
                    velocidade += 5
                acelera = 0
        posY += velocidadeCarros
        py -= velocidadeCarros
    else:
        Fecho = True
    # Adiciona as imagens
    janela.blit(fundo, (0, 0))
    janela.blit(carro, (x, y))
    janela.blit(carroAzul, (posX, posY))
    janela.blit(carroBranco, (px, py))
    janela.blit(texto, PosTexto)
    if colisao3 and not Fecho:
        if tempoTr == 0:
            Fecho = True
        TextTransicao = fontTrans.render(f'Saia da transição em: {tempoTr} S', True, (255, 0, 0))
        janela.blit(TextTransicao, PosTransicao)
        if timer2 <= 20:
            timer2 += 1
        else:
            tempoTr = tempo - tempo + cod
            cod -= 1
            timer2 = 0
    else:
        tempoTr = 5
        cod = 5
    if Fecho:
        janela.blit(GameOver, PosGameOver)
        TextTransicao = fontTrans.render(f'Jogar novamente (Espaço)', True, (255, 255, 255), (0, 0, 0))
        janela.blit(TextTransicao, (300, 400))
        comandos = pygame.key.get_pressed()
        if comandos[pygame.K_SPACE]:
            Fecho = False
            x = 210  # max 500 min 210
            y = 200
            posX = 250
            posY = -300
            px = 450
            py = 1500
            timer = tempo = acelera = 0
            velocidade = 10
            velocidadeCarros = 5
            
    # Atualiza a janela
    pygame.display.update()
pygame.quit()
