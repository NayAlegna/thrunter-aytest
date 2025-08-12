$REPORT_TEXT
REPORT_EOF 

# Substitute variables in the file using safer approach
sed -i "s/HUNT_NAME_PLACEHOLDER/$HUNT_NAME/g" "$FILENAME"
sed -i "s/HUNT_ID_PLACEHOLDER/$HUNT_ID/g" "$FILENAME"
sed -i "s/HUNT_TYPE_PLACEHOLDER/$HUNT_TYPE/g" "$FILENAME"
sed -i "s/ISSUE_NUMBER_PLACEHOLDER/$ISSUE_NUMBER/g" "$FILENAME"
sed -i "s|ISSUE_URL_PLACEHOLDER|$ISSUE_URL|g" "$FILENAME"
sed -i "s/ISSUE_AUTHOR_PLACEHOLDER/$ISSUE_AUTHOR/g" "$FILENAME"
sed -i "s/ISSUE_CREATED_PLACEHOLDER/$ISSUE_CREATED/g" "$FILENAME"
sed -i "s/ISSUE_UPDATED_PLACEHOLDER/$ISSUE_UPDATED/g" "$FILENAME"
sed -i "s/PRIORITY_PLACEHOLDER/$PRIORITY/g" "$FILENAME"
sed -i "s/DURATION_PLACEHOLDER/$DURATION/g" "$FILENAME"

# Handle multi-line substitutions more safely
echo "Substituting issue body..."
# Create temp file for issue body
echo "$ISSUE_BODY" > /tmp/issue_body.txt
awk '
  /ISSUE_BODY_PLACEHOLDER/ { 
    while ((getline line < "/tmp/issue_body.txt") > 0) print line
    close("/tmp/issue_body.txt")
    next 
  }
  { print }
' "$FILENAME" > "${FILENAME}.tmp" && mv "${FILENAME}.tmp" "$FILENAME"

echo "Substituting data sources..."
echo "$DATA_SOURCES" > /tmp/data_sources.txt
awk '
  /DATA_SOURCES_PLACEHOLDER/ { 
    while ((getline line < "/tmp/data_sources.txt") > 0) print line
    close("/tmp/data_sources.txt")
    next 
  }
  { print }
' "$FILENAME" > "${FILENAME}.tmp" && mv "${FILENAME}.tmp" "$FILENAME"

echo "Substituting hunter notes..."
echo "$HUNTER_NOTES" > /tmp/hunter_notes.txt
awk '
  /HUNTER_NOTES_PLACEHOLDER/ { 
    while ((getline line < "/tmp/hunter_notes.txt") > 0) print line
    close("/tmp/hunter_notes.txt")
    next 
  }
  { print }
' "$FILENAME" > "${FILENAME}.tmp" && mv "${FILENAME}.tmp" "$FILENAME"

# Clean up temp files
rm -f /tmp/issue_body.txt /tmp/data_sources.txt /tmp/hunter_notes.txt

echo "Generated threat hunt report: $FILENAME"

# Store filename for next step
echo "REPORT_FILENAME=$FILENAME" >> $GITHUB_ENV
