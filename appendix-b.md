# Appendix B: Troubleshooting Guide

Common issues and solutions.

## Issue: API Key Not Found

**Error:** `GEMINI_API_KEY environment variable is not set`

**Solution:**
```bash
export GEMINI_API_KEY=your-key-here
```

## Issue: ONNX Logs

**You see:** ONNX runtime logs during startup

**Solution:** This is normal. The embedding model is initializing.

## Issue: Poor RAG Results

**Problem:** RAG returns irrelevant results

**Solutions:**
- Lower `min-score` threshold
- Check if embedding model supports your language
- Use tools instead for exact queries
- Improve data formatting

## Issue: Memory Issues

**Problem:** Out of memory errors

**Solutions:**
- Reduce `MAX_MESSAGES_PER_SESSION`
- Clean up expired sessions
- Use persistent embedding store

## Issue: Gemini Function Calling Error

**Error:** "Please ensure that function call turn comes immediately after a user turn"

**Solution:**
- Reduce memory size (MAX_MESSAGES_PER_SESSION = 10)
- Clear memory when it gets too large
- Rebuild assistant on fresh memory

## Issue: Mermaid Not Rendering

**Problem:** Diagrams don't show

**Solutions:**
- Check Mermaid version (use v10)
- Initialize Mermaid after React renders
- Use custom marked renderer

## Quick Reference

### Common Fixes

1. **API Key:** Set environment variable
2. **ONNX Logs:** Normal, ignore
3. **Poor Results:** Adjust thresholds, use tools
4. **Memory:** Reduce size, cleanup
5. **Function Calls:** Reduce memory size

## Next Steps

1. **Appendix C**: Example Projects

## Key Takeaways

✅ **Check environment variables** first  
✅ **ONNX logs are normal**  
✅ **Adjust thresholds** for better results  
✅ **Reduce memory** for Gemini  

**Remember:** Most issues have simple solutions!

---

## Navigation

| [← Previous](appendix-a) | [Home](home) | [Next →](appendix-c) |
|:---|:---:|---:|
| Appendix A: LangChain4j API Reference | Building Agentic AI Applications with Java | Appendix C: Example Projects |

