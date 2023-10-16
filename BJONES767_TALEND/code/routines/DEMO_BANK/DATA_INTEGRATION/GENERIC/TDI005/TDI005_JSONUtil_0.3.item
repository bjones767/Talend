package routines;

import org.json.simple.JSONArray; 
import org.json.simple.JSONObject; 
import org.json.simple.parser.ContainerFactory;
import org.json.simple.parser.*; 

import java.io.File;
import java.io.FileInputStream;
import java.io.InputStream;
import org.apache.commons.io.IOUtils;

import java.util.*;

public class TDI005_JSONUtil {
	
	private static Set<String> keys;
	private static Set<String> objects;
	private static Set<String> paths;
	private static final int TABLE_PREFIX_LENGTH = 9;
	/*
	public static ContainerFactory createContainerFactory() {
	    return new ContainerFactory() {
	        @Override
	        public Map createObjectContainer() {
	            return new LinkedHashMap();
	        }

	        @Override
	        public List creatArrayContainer() {
	            return new LinkedList();
	        }
	    };
	}
	*/
	public static void parseJSONFromPath(JSONObject JSON, String object, String path) throws Exception {
		if("root".equals(path)) parseJSON(JSON, object, "root");	
		else parseJSON(((JSONObject)JSON.get(path)), object, path);
	}
	
    public static void parseJSON(JSONObject JSON, String object, String path) throws Exception {
    	setKeys();
    	setObjects();
    	setPaths();
    	Set<String> entries = JSON.keySet();
    	for(String key : entries) {												//Loop through keys
    		Object obj = JSON.get(key);												//Get the value of the key to determine hierarchy
    		if (obj instanceof JSONObject) {										//Check if the value is an object
    			objects.add(key);															//Add to list of objects
    			parseJSON((JSONObject)obj, key, path+"."+key);								//Recurse this function for the child object
    		} else
    		if (obj instanceof JSONArray && ((JSONArray) obj).size() > 0) {			//Check if the value is an array
				if (((JSONArray) obj).get(0) instanceof JSONObject){ 					//Check if it is an array of objects
	    			objects.add(key);															//Add to list of objects
	    			for(int i = 0; i < ((JSONArray) obj).size(); i++) {						//Go through each item in the array
	    				JSONObject jo = (JSONObject)((JSONArray) obj).get(i);
	
	    				parseJSON(jo, key, path+"."+key);										//Recurse this function for each item
	    			}
				} else {																//Not an object array
					paths.add(path);														//Add path to list
					if(object != null) key = object +"."+ key;								//Add key to list
	    			keys.add(key);
				}
    		} else {																//Not an object or array
    			paths.add(path);															//Add path to list
    			if(object != null) key = object +"."+ key;									//Add key to list
    			keys.add(key);
    		}
    	}
    }
    
    public static ArrayList<String[]> getUUIDPathAndData(JSONObject JSON, String object, String UUID) {
    	Set<String> entries = JSON.keySet();
    	ArrayList<String[]> data = new ArrayList<String[]>();
    	if(UUID == null) {
			UUID = java.util.UUID.randomUUID().toString();
		}
    	for(String key : entries) {
    		Object obj = JSON.get(key);
    		if (obj instanceof JSONObject) {
    			data.addAll(getUUIDPathAndData((JSONObject)obj , object+"."+key, java.util.UUID.randomUUID().toString()));
    			if(objectNoData(JSON)) {
    				data.add(new String[]{UUID, object +".#REMOVE#", "#REMOVE#"});
    			}
    		} else
        	if (obj instanceof JSONArray) {
        		if ( !((JSONArray) obj).isEmpty() && ((JSONArray) obj).get(0) instanceof JSONObject){
        			for(int i = 0; i < ((JSONArray) obj).size(); i++) {	
        				JSONObject jo = (JSONObject)((JSONArray) obj).get(i);
        				data.addAll(getUUIDPathAndData(jo, object+"."+key+"["+i+"]", java.util.UUID.randomUUID().toString()));
        			}
        		} else {
        			if (JSON.get(key) instanceof JSONArray) {
        				data.add(new String[]{UUID, object+"."+key, JSON.get(key).toString()});
        			} else {
        				data.add(new String[]{UUID, object+"."+key, (String)JSON.get(key)});
        			}
        		}
        	} else {
        		data.add(new String[]{UUID, object+"."+key, (String)JSON.get(key)});
        	}
    	}
    	
    	
    	return data;
    }
    
    public static boolean objectNoData(JSONObject JSON) {
    	Set<String> entries = JSON.keySet();
    	boolean is = true;
    	if (entries.isEmpty()) return is;
    	for(String key : entries) {
    		Object obj = JSON.get(key);
    		if (!(obj instanceof JSONObject) && !(obj instanceof JSONArray)) {
    			return !is;
    		} 
    	}
    	return is;
    }
    
    public static String[] getCrossRef(String path) {
    	String s = null;
    	String sarray[] = path.split("\\.");
    	
    	if(sarray == null || sarray.length < 2)
    		return null;
    		
		String a1 = sarray[sarray.length -2];
		String a2 = sarray[sarray.length -1];
		s = a1 + "_to_" + a2;
		
    	if(s != null && s.length() > (64 - TABLE_PREFIX_LENGTH)){
    		//shorten name
    		int diff = (64 - 9) - s.length();
    		int i = Math.round(diff/2);
    		
    		if(a1.length() > diff && a2.length() > diff){
    			s = a1.substring(0,i) + "_to_" + a2.substring(0,i);
    			return new String[]{s, a1, a2};
    		}
    		if(a1.length() > i && a2.length() > i){
    			s = a1.substring(0,i) + "_to_" + a2.substring(0,i);
    		} else {
    			if(a1.length() > diff) s = a1.substring(0,diff) +"_to_"+ a2;
    			else if(a2.length() > diff) s = a1 + "_to_" + a2.substring(0,diff);
    			
    		}
    	}
    	return new String[]{s, a1, a2};
    }
    
    public static String[] getLastHierarchySet(String path) {
    	String sarray[] = path.split("\\.");
    	
    	if(sarray == null || sarray.length < 2)
    		return null;
    	
    	return new String[]{sarray[sarray.length -2], sarray[sarray.length -1]};
    	
    }
   
    
    public static void printKeys() {
    	for(String s : keys) {
    		System.out.println(s);
    	}
    }
    
    public static Set<String> getKeys() {
    	return keys;
    }
    
    public static void setKeys() {
    	if(keys == null) {
    		keys = new HashSet<String>();
    	}
    }
    
    public static void printObjects() {
    	for(String s : objects) {
    		System.out.println(s);
    	}
    }
    
    public static Set<String> getObjects() {
    	return objects;
    }
    
    
    public static void setObjects() {
    	if(objects == null) {
    		objects = new HashSet<String>();
    		objects.add("root");
    	}
    }
    
    public static void printPaths() {
    	for(String s : paths) {
    		System.out.println(s);
    	}
    }
    
    public static Set<String> getPaths() {
    	return paths;
    }
    
    
    public static void setPaths() {
    	if(paths == null) {
    		paths = new HashSet<String>();
    	}
    }
}
