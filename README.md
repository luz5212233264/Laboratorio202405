# Laboratorio202405
import pandas as pd
import matplotlib.pyplot as plt
import smtplib
from email.mime.multipart import MIMEMultipart
from email.mime.base import MIMEBase
from email.mime.text import MIMEText
from email import encoders
import os

url = "https://raw.githubusercontent.com/rrendon502/polars_learning/main/US_PopTotal_20230713030810%203.csv"
df = pd.read_csv(url)

df = df.drop(columns=["Absolute value in thousands Missing value"])

df = df[["Year", "Economy", "Economy Label", "Absolute value in thousands"]]

df.columns = ["Año", "Economía", "Etiqueta de Economía", "Valor Absoluto en miles"]

datos_gua_sal = df[(df["Economía"] == "Guatemala") | (df["Economía"] == "El Salvador")]
print(datos_gua_sal)

datos_guatemala = df[df["Economía"] == "Guatemala"]
plt.figure(figsize=(10, 5))
plt.plot(datos_guatemala["Año"], datos_guatemala["Valor Absoluto en miles"], marker='o', linestyle='-')
plt.title('Desarrollo Económico de Guatemala')
plt.xlabel('Año')
plt.ylabel('Valor Absoluto en miles')
plt.grid(True)
plt.savefig("desarrollo_economico_guatemala.png")
plt.show()


excel_file = "Laboratorio_20240522.xlsx"
datos_gua_sal.to_excel(excel_file, index=False)


def enviar_correo(archivo_adjunto):
    remitente = "lramirezs12@miumg.edu.gt"
    destinatario = "rendonp@miumg.edu.gt"
    asunto = "Laboratorio 20240522"
    cuerpo = "Por favor, encuentre adjunto el archivo Excel solicitado."
    
    
    smtp_server = "smtp.gmail.com"
    smtp_port = 587
    smtp_usuario = "lramirezs12@miumg.edu.gt" 
    smtp_contraseña = "123*admi"  

    
    mensaje = MIMEMultipart()
    mensaje['From'] = remitente
    mensaje['To'] = destinatario
    mensaje['Subject'] = asunto
    mensaje.attach(MIMEText(cuerpo, 'plain'))

    
    adjunto = MIMEBase('application', 'octet-stream')
    with open(archivo_adjunto, 'rb') as archivo:
        adjunto.set_payload(archivo.read())
    encoders.encode_base64(adjunto)
    adjunto.add_header('Content-Disposition', f'attachment; filename={os.path.basename(archivo_adjunto)}')
    mensaje.attach(adjunto)

    
    try:
        servidor = smtplib.SMTP(smtp_server, smtp_port)
        servidor.starttls()
        servidor.login(smtp_usuario, smtp_contraseña)
        servidor.send_message(mensaje)
        servidor.quit()
        print("Correo enviado exitosamente")
    except Exception as e:
        print(f"Error al enviar el correo: {e}")


enviar_correo(excel_file)
