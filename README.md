# AI-CHAT-APP
import okhttp3.*
import org.json.JSONObject
import java.io.IOException

class GPT4Client {
    private val client = OkHttpClient()

    fun sendMessage(message: String, callback: (response: String?) -> Unit) {
        val apiKey = "your-openai-api-key"
        val json = JSONObject()
        json.put("model", "gpt-4")
        json.put("prompt", message)
        json.put("max_tokens", 150)

        val body = RequestBody.create(MediaType.parse("application/json; charset=utf-8"), json.toString())

        val request = Request.Builder()
            .url("https://api.openai.com/v1/completions")
            .post(body)
            .addHeader("Authorization", "Bearer $apiKey")
            .build()

        client.newCall(request).enqueue(object : Callback {
            override fun onFailure(call: Call, e: IOException) {
                callback(null)
            }

            override fun onResponse(call: Call, response: Response) {
                response.body()?.let {
                    val responseData = JSONObject(it.string())
                    val chatResponse = responseData.getJSONArray("choices").getJSONObject(0).getString("text")
                    callback(chatResponse)
                }
            }
        })
    }
}
