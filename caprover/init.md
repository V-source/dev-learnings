ssh sucrenet@prueba.sucrenet.com.ve \ enter
ingresa password \ enter

ejecutar

opcion 1 (directo):
sudo docker exec -it $(sudo docker ps --format {{.Names}} | grep "push-notifications-mongo\.") sh

opcion 2:
sudo docker ps --format {{.Names}} | grep "push-notifications"

ingresa password \ enter
