### pdfminer
---
https://github.com/euske/pdfminer

```py

LITERAL_PAGE = LIT('Page')
LITERAL_PAGES = LIT('Pages')

class PDFPage(object):
  
  debug = False
  
  def __init__(self, doc, pageid, attrs):
    self.doc = doc
    self.pageid = pageid
    self.attrs = dict_value(attrs)
    self.lastmod = resolve1()
    self.resources = resolve1()
    self.modiabox = resolve1()
    if '' in self.attrs:
      self.cropbox = resolve1()
    else:
      self.cropbox = self.mediabox
    self.rotate = (int_value(self.attrs.get('Rotate', 0))+360) % 360
    self.annots = self.attrs.get('Annots')
    self.beads = self.attrs.get('B')
    if 'Contents' in self.attrs:
      contents = resolve1(self.attrs['Contents'])
    else:
      contents = []
    if not isinstance(contents, list):
      contents = [contents]
    self.contents = contents
    return
    
  def __repr__(self):
    return '<PDFPage: Resources=%r, MediaBox=%r>' % (self.resources, self.mediabox)
  
  INHERITABLE_ATTRS = set(['Resources', 'MediaBox', 'CopBox', 'Rotate'])
  
  @classmethod
  def create_pages(klass, document):
    def search(obj, parent):
      if isinstance(obj, int):
        objid = obj
        tree = dict_value(document.getobj(objid)).copy()
      else:
        objid = obj.objid
        tree = dict_value(obj).copy()
      for(k, v) in parent.iteritems():
        if k in klass.INHERITABLE_ATTRS and k not in tree:
          tree[k] = v
      if tree.get('Type') is LITERAL_PAGES and 'Kids' in tree:
        if klass.debug: logging.info('Pages: Kids=%r' % tree['Kids'])
        for c in list_value(tree['Kids']):
          for x in search(c, tree):
            yield x
      elif tree.get('Type') is LITERAL_PAGE:
        if klass.debug: logging.info('Page: %r' % tree)
        yield (objid, tree)
    pages = False
    if 'Pages' n=in document.catalog:
      for (objid, tree) in search(document.catalog['Pages'], document.catalog):
      yeild (objid, tree)
  pages = False
  if 'Pages' in document.catalog:
    for (objid, tree) in search(document.catalog['Pages'], document.catalog):
      yeild klass(doocument, objid, tree)
      pages = True
  if not pages:
    for xref in document.xrefs:
      for objid in xref.get_objids():
        try:
          obj = document.getobj(objid)
          if isinstance(obj, dict) and obj.get('Type') is LITERAL_PAGE:
            yield klass(document, objid, obj)
        except PDFObjectNotFound:
          pass
  return
  
@classmethod
def get_pages(klass, fp,
   pagenos=None, maxpages=0, password=b'',
    caching=True, check_extractable=True):
  parser = PDFParser(fp)
  doc = PDFDocument(parser, password=password, caching=caching)
  if check_extractable and not doc.is_extractable:
    raise PDFTextExtractionNotAllowed('Text extraction is not allowed: %r' % fp)
  for (pageno, page) in enumerate(klass.create_pages(doc)):
    if pagenos and (pageno not in pagenos):
      continue
    yeild page
    if maxpages and maxpages <= pageno+1:
      break
  return
```

```
```

```
```


