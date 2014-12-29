---
layout:post
published:true
---

Valves provide a means to insert logic and specific handling, into the request processing pipeline. When we need to add something funcationality or check before the request reaches the server in that case, we need to add the same using a valve.

Valves work at the server level, the same as filters do at the application level. Although it is possible to tie a valve implementation to a context root to specify the application for which it will be inserted in the request processing pipeline.

A typical use case for a valve, is for SSO and authentication needs. Also a very valid and known example is AccessLogValve.

A custom valve can be implemented as below :
<pre><code>
import java.io.IOException;
import javax.servlet.ServletException;
import org.apache.catalina.connector.Request;
import org.apache.catalina.connector.Response;
import org.apache.catalina.valves.ValveBase;

public class CustomValve extends ValveBase {
   
    @Override
    public void invoke(Request request, Response response) throws IOException,
    ServletException {
        try {
        //if you want to add something to request
        request.getCoyoteRequest().getMimeHeaders().setValue("somekey").setString(somevalue);
            getNext().invoke(request, response);
        } finally {
            //TODO cleanup
        }
    }

}
</code></pre>

After creating the valve it needs to be registered to the server runtime using the application deployment descriptor. 
For example in case of jboss, the same can be put in the jboss-web.xml as below:
<pre><code>
&lt;jboss-web&gt;
    &lt;context-root&gt;/&lt;/context-root&gt;
    &lt;valve&gt;
      &lt;class-name&gt;CustomValve&lt;/class-name&gt;
    &lt;/valve&gt;
&lt;/jboss-web&gt;
</code></pre>

This would map the valve to the context-root of the application, whose deplyoment descriptor contains the required entry.

Reference : http://tomcat.apache.org/tomcat-7.0-doc/config/valve.html#Introduction
