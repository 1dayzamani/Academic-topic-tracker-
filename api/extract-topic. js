export default async function handler(req, res) {
  if (req.method !== "POST") {
    return res.status(405).json({ error: "Method not allowed" });
  }

  try {
    const { content } = req.body;

    const response = await fetch("https://api.anthropic.com/v1/messages", {
      method: "POST",
      headers: {
        "Content-Type": "application/json",
        "x-api-key": process.env.ANTHROPIC_API_KEY,
        "anthropic-version": "2023-06-01",
      },
      body: JSON.stringify({
        model: "claude-sonnet-4-6",
        max_tokens: 1200,
        messages: [{ role: "user", content }],
      }),
    });

    const data = await response.json();
    const text = data.content?.map((b) => b.text || "").join("\n") || "";
    const clean = text.replace(/```json|```/g, "").trim();
    const topics = JSON.parse(clean);

    res.status(200).json({ topics });
  } catch (err) {
    console.error(err);
    res.status(500).json({ error: "Failed to extract topics" });
  }
}
