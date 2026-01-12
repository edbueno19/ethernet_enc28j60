/*
 * Componente Ethernet ENC28J60 (ESP-IDF)
 *
 * Alvos suportados:
 *   ESP32 | ESP32-C2 | ESP32-C3 | ESP32-S2 | ESP32-S3
 *
 * Componente Ethernet reutilizável para ESP-IDF baseado no
 * controlador Ethernet SPI ENC28J60.
 *
 * ================================================================
 * VISÃO GERAL
 * ================================================================
 *
 * O ENC28J60 é um controlador Ethernet independente com interface SPI.
 *
 * Este componente expõe uma única função pública de inicialização:
 *
 *      void ethernet_init(void);
 *
 * A chamada dessa função inicializa completamente a pilha Ethernet:
 *  - Barramento SPI e dispositivo ENC28J60
 *  - Drivers MAC e PHY Ethernet
 *  - Interface de rede TCP/IP (esp_netif)
 *  - Handlers de eventos Ethernet e IP
 *  - Inicialização do driver Ethernet
 *
 * Nenhum código específico de Ethernet é necessário no main.c
 * além da chamada para ethernet_init().
 *
 * ================================================================
 * ESTRUTURA DO COMPONENTE
 * ================================================================
 *
 * enc28j60/
 * ├── enc28j60.c
 * ├── enc28j60.h
 * ├── esp_eth_enc28j60.h
 * ├── esp_eth_mac_enc28j60.c
 * ├── esp_eth_phy_enc28j60.c
 * ├── Kconfig.projbuild
 * └── README.md
 *
 * ================================================================
 * COMO UTILIZAR
 * ================================================================
 *
 * 1) Adicione o componente:
 *
 *    your_project/components/enc28j60
 *
 * 2) Inclua o header:
 *
 *    #include "esp_eth_enc28j60.h"
 *
 * 3) Inicialize o Ethernet:
 *
 *    void app_main(void)
 *    {
 *        ethernet_init();
 *    }
 *
 * Após essa chamada, a interface Ethernet estará pronta e
 * obterá automaticamente um endereço IP via DHCP.
 *
 * ================================================================
 * REQUISITOS DE HARDWARE
 * ================================================================
 *
 *  - Placa de desenvolvimento ESP32
 *  - Módulo Ethernet ENC28J60 (revisão 6 recomendada)
 *  - Fonte de alimentação estável
 *
 * O ENC28J60 pode consumir mais de 200 mA em picos de transmissão.
 *
 * Opções recomendadas de alimentação:
 *  - USB 3.0
 *  - Fonte externa de 5 V (>= 1 A)
 *  - Regulador externo de 3,3 V para o ENC28J60 (GND comum)
 *
 * Alimentação insuficiente pode causar erros de leitura SPI
 * ou comportamento indefinido do ENC28J60.
 *
 * ================================================================
 * ATRIBUIÇÃO PADRÃO DE PINOS
 * ================================================================
 *
 *   GPIO ESP32   | ENC28J60
 *  --------------+-----------
 *   GPIO14       | SPI_CLK
 *   GPIO13       | SPI_MOSI
 *   GPIO12       | SPI_MISO
 *   GPIO15       | SPI_CS
 *   GPIO4        | INT
 *
 * Todos os GPIOs podem ser configurados via menuconfig.
 *
 * ================================================================
 * CONFIGURAÇÃO
 * ================================================================
 *
 * Execute:
 *
 *    idf.py menuconfig
 *
 * Navegue até:
 *
 *    Ethernet Configuration -> ENC28J60 Configuration
 *
 * Opções disponíveis:
 *  - Host SPI
 *  - GPIOs MISO / MOSI / SCLK / CS
 *  - GPIO de interrupção
 *  - Frequência do clock SPI
 *  - Modo duplex (Half / Full)
 *
 * ================================================================
 * OBSERVAÇÕES SOBRE O CLOCK SPI
 * ================================================================
 *
 *  - O ENC28J60 suporta clocks SPI de até 20 MHz
 *  - Frequência mínima recomendada: 8 MHz
 *  - Valor padrão: 8 MHz
 *
 * ================================================================
 * OBSERVAÇÕES IMPORTANTES
 * ================================================================
 *
 * Endereço MAC:
 *  - O ENC28J60 não possui endereço MAC gravado de fábrica
 *  - Um MAC localmente administrado é atribuído internamente
 *
 * Modo Duplex:
 *  - O modo full-duplex é recomendado
 *  - Não há suporte à auto-negociação
 *  - O switch/roteador remoto deve ser configurado manualmente
 *
 * Exemplo no Linux:
 *
 *    sudo ethtool -s eth0 speed 10 duplex full autoneg off
 *
 * Cabeamento:
 *  - Utilize fios SPI curtos e com comprimentos semelhantes
 *  - Evite cruzamento de fios
 *  - Garanta uma boa conexão de terra (GND)
 *
 * Tempo de retenção do CS:
 *  - Calculado automaticamente utilizando:
 *
 *      enc28j60_cal_spi_cs_hold_time()
 *
 *  - Ajuste cs_ena_posttrans caso o layout da PCB exija
 *
 * ================================================================
 * REUTILIZAÇÃO EM APLICAÇÕES DE REDE
 * ================================================================
 *
 * Após a chamada de ethernet_init(), a interface Ethernet
 * estará totalmente operacional.
 *
 * É seguro inicializar:
 *  - Clientes MQTT
 *  - Sockets TCP/UDP
 *  - Clientes ou servidores HTTP
 *  - Qualquer serviço de rede baseado em lwIP
 *
 * Nenhuma configuração adicional de Ethernet é necessária.
 *
