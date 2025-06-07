# REGRAS

## Simulador do Sensor

A cada 15 segundos, sorteia pseudoaleatoriamente entre 0 e 100, onde:

* sensor < 50 = NORMAL (menos de 50mm de chuva acumulada, não gerando alagamentos)
* sensor >= 50 = CRITICO (mais 50mm de chuva acumulada, gerando alagamentos)

Após a previsão, esse resultado é enviado para o orquestrador

## CNN

Faz a inferência de uma imagem aleatória no nosso banco, onde

* SEM_RISCO = Não há pessoas em risco
* COM_RISCO = Há pessoas em risco

## Orquestrador

Ele recebe os resultados do sensor e da cnn e faz as gestões:

* Caso receba valor "NORMAL" do sensor, não há alagamento. Sendo assim, a CNN não é acionada, o mapa "normalidade" é apresentado no dashboard nenhum aviso é enviado.

* Caso receba valor "CRITICO" do sensor, há alamameto. Sendo assim, a CNN é acionada.

* Caso a inferência dê "SEM_RISCO", não temos pessoas em risco. Sendo assim, o mapa "alerta" é apresentado no dashboard, mas nenhum aviso é enviado.

* Caso a inferência dê "COM_RISCO", temos pessoas em risco. Sendo assim, o mapa "perigo" é apesentado no dashboard, e os avisos são enviados.

## Obs

Cada loop simboliza 1 dia
