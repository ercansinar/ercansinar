import torch
import transformers
import requests

# Veri kümesi
data = [
    "Bugün hava çok güzel.",
    "İstanbul'da yaşıyorum.",
    "Python'da kod yazmayı seviyorum.",
    "Makine öğrenimi ile ilgileniyorum.",
]

# Büyük dil modeli
model = transformers.BardModel.from_pretrained("google/bard")

# Web uygulaması
app = Flask(__name__)

@app.route("/")
def index():
    input_text = request.args.get("text")
    output_text = model.predict(input_text)

    # Arama motorlarından bilgi al
    query = f"{input_text} wikipedia"
    response = requests.get(f"https://en.wikipedia.org/w/api.php?action=query&format=json&prop=extracts&exintro=&titles={query}")
    data = response.json()["query"]["pages"][query]["extract"]

    # Bilgiyi çıktıya ekle
    output_text += "\n\n**Bilgi:**\n" + data

    return jsonify({"output": output_text})

if __name__ == "__main__":
    app.run()
