idf_component_register(
    SRCS "ESP32_Cam.c"
    PRIV_REQUIRES spi_flash nvs_flash
    PRIV_REQUIRES nvs_flash esp_psram
    PRIV_REQUIRES esp_http_server
    PRIV_REQUIRES esp_timer
    PRIV_REQUIRES esp_netif 
    PRIV_REQUIRES esp_wifi
    PRIV_REQUIRES esp_event
    INCLUDE_DIRS "."
)