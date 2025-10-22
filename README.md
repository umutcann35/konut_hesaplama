# konut_hesaplama

import streamlit as st

st.title("Konut Birikimi Hesaplama")


baslangic_yasi = st.number_input("Başlangıç Yaşı", 18, 60, 25)
bitis_yasi = st.number_input("Bitiş Yaşı", baslangic_yasi + 1, 80, 65)
aylik_birikim = st.number_input("Aylık Birikim TL", 0, 1000000, 3000)
daire_fiyati = st.number_input("Daire Fiyatı TL", 100000, 10000000, 1250000)
aylik_kira = st.number_input("Aylık Kira TL", 0, 100000, 8000)
pesinat_orani = st.slider("Peşinat Oranı TL", 0, 100, 20) / 100
kredi_yil = st.slider("Kredi Süresi (Yıl)", 1, 30, 15)
faiz_orani = st.number_input("Kredi Faizi (%)", 0.0, 100.0, 0.0) / 100


if st.button("Hesapla"):
    ay_sayisi = (bitis_yasi - baslangic_yasi) * 12
    nakit = 0
    ev_sayisi = 0
    krediler = []
    pesinat = daire_fiyati * pesinat_orani

    for ay in range(1, ay_sayisi + 1):
        kira = ev_sayisi * aylik_kira
        toplam_taksit = sum(t for _, t in krediler)
        krediler = [(k - 1, t) for k, t in krediler if k > 1]
        nakit += aylik_birikim + kira - toplam_taksit

        if nakit >= pesinat:
            nakit -= pesinat
            ev_sayisi += 1
            anapara = daire_fiyati - pesinat
            vade = kredi_yil * 12
            if faiz_orani == 0:
                taksit = anapara / vade
            else:
                r = faiz_orani / 12
                taksit = anapara * (r * (1 + r) ** vade) / ((1 + r) ** vade - 1)
            krediler.append((vade, taksit))


    st.subheader("Sonuçlar")
    st.write(f"Toplam Ev Sayısı: **{ev_sayisi}**")
    st.write(f"Aylık Kira Geliri: **{ev_sayisi * aylik_kira:,.0f} TL**")
    st.write(f"Simülasyon Sonu Nakit: **{nakit:,.0f} TL**")
