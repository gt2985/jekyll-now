---
layout: post
title: XML  Handling invalid  characters
published: true
---

For a requirement, had to recently make sure that the input data that comes in as a json body needs to be converted into xml to make subequent calls to other services.
It landed up in a weird requirement, where data that could be valid for JSON, could prove to be invalid for an xml. 

So needed to make sure to remove the invalid xml characters coming into the input request.

So I decided to use precompiled regex to validate if the request had any invalid xml characters.

<pre><code>
/**
     * Regex pattern for invalid xml characters as defined in the XML 1.0 specification.
     */
    private static final String xml10pattern = "[^"
            + "\u0009\r\n"
            + "\u0020-\uD7FF"
            + "\uE000-\uFFFD"
            + "\ud800\udc00-\udbff\udfff"
            + "]";
</code></pre>

And a simple method to validate the input payload.

<pre><code>
/**
     * This method checks if the input String has only
     * valid XML unicode characters as specified by the
     * XML 1.0 standard. For reference, please see
     * <a href="http://www.w3.org/TR/2000/REC-xml-20001006#NT-Char">the standard</a>. 
     * This method will return null if the input is null or empty.
     *
     * @param inputData The String to check for invalid XML characters.
     * @return String, invalid xml character or null if xml only has 
     * valid characters.
     */
    private String hasInValidXmlCharacterData(final String inputData) {
    	String invalidStringinXml = null;
    	if (null != inputData) {
    		Matcher invalidXMlMatcher = xml10InvalidPattern.matcher(inputData);
    		if (invalidXMlMatcher.find()) {
    			invalidStringinXml = invalidXMlMatcher.group();
    		}
    	}
    	return invalidStringinXml; 
    }   
</code></pre>

Still for some reason the validation will always fail when i`ll send it out as a curl request. It would work from a main program or a junit for the same method, but the curl request would fail. 

After some head scratching, I came to understand that I have to unescape the characters in the input payload. 
Thus after changing the method above to 

<pre><code>
/**
     * This method checks if the input String has only
     * valid XML unicode characters as specified by the
     * XML 1.0 standard. For reference, please see
     * <a href="http://www.w3.org/TR/2000/REC-xml-20001006#NT-Char">the standard</a>. 
     * This method will return null if the input is null or empty.
     *
     * @param inputData The String to check for invalid XML characters.
     * @return String, invalid xml character or null if xml only has 
     * valid characters.
     */
    private String hasInValidXmlCharacterData(final String inputData) {
    	String invalidStringinXml = null;
    	if (null != inputData) {
    		Matcher invalidXMlMatcher = xml10InvalidPattern.matcher(StringEscapeUtils.unescapeJava(inputData));
    		if (invalidXMlMatcher.find()) {
    			invalidStringinXml = invalidXMlMatcher.group();
    		}
    	}
    	return invalidStringinXml; 
    }   
</code></pre>

StringEscapeUtils from apache commons comes to rescue here
